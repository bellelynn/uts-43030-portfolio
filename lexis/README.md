# Goal 4 — Full-stack development on the JavaScript ecosystem (LEXIS)

[← Back to portfolio home](../)

## What I set out to do — and the pivot from mobile to web

**Original plan (in my PLP):** Learn React (web) and React Native (mobile) on top of my existing C# / Python back-end, with the goal of becoming employable for the front-end-heavy roles at both Fire Front and Holocentric.

**What I actually did:** Pivoted to a JavaScript-everywhere stack — React on the web, Node.js / Express on the back-end, PostgreSQL underneath — and built LEXIS as a complete full-stack web application.

### Why I pivoted

Two things shifted during the semester.

**First**, I realised that learning the JavaScript ecosystem end-to-end — front-end framework plus a Node-based back-end — was a more honest match for the actual job market than learning React on top of a C# back-end. The Holocentric Fullstack ad listed Node alongside React; Fire Front's stack is JavaScript-first. Staying inside one language ecosystem also reduced the integration friction so I could spend my hours on actual feature development rather than on toolchain glue.

**Second**, I decided to prioritise depth on web over breadth across web and mobile. React Native is on the roadmap, but in 70 hours I could either build something credible on the web or build something shallow on both. I chose depth — and shipped LEXIS as a result.

---

## What LEXIS is

LEXIS is a single-page web application for managing legal cases in a law firm. Lawyers and case managers can create, view, edit, and delete cases; assign lawyers to cases; track case status; and monitor firm-wide statistics through an interactive dashboard. Four core workflows working end-to-end:

1. **Case listing** — sortable, filterable table with search by name, client, or lawyer; filter by status
2. **Case detail with reassignment** — single-case view with documents, time entries, and the ability to reassign lawyers atomically through a transaction
3. **Client and lawyer management** — full CRUD with role-based access control enforced at the SQL level (see [Goal 1](../ethics-privacy/) for details)
4. **Statistics dashboard** — real-time totals (total cases, in-progress, closed, estimated revenue) and a 6-month case growth chart

---

## Tech stack

| Layer | Technology |
|---|---|
| **Front-end** | React 19, Vite, Ant Design 6, ECharts |
| **Routing** | React Router DOM v7 (client-side SPA routing) |
| **HTTP client** | Axios |
| **Back-end** | Node.js, Express 5 |
| **Auth** | JWT bearer tokens |
| **Database driver** | `pg` (raw PostgreSQL, no ORM) |
| **Database** | PostgreSQL (5 normalised tables with FK constraints) |
| **Tooling** | Git, GitHub, VS Code, DBngin / Homebrew Postgres, Postman |

---

## Architecture

```mermaid
flowchart LR
    subgraph FE["React 19 + Vite (SPA)"]
        AD["Ant Design 6 components"]
        EC["ECharts (dashboard)"]
        RR["React Router DOM v7"]
        ST["useState / Context"]
    end

    subgraph BE["Node.js / Express 5 REST API"]
        JWT["JWT auth middleware"]
        VAL["Body-size + content-type validation"]
        RL["Per-IP rate limiter"]
        RBAC["RBAC enforced in SQL queries"]
    end

    subgraph DB["PostgreSQL"]
        T1["Office"]
        T2["Lawyer (FK Office)"]
        T3["Client"]
        T4["Case (FK Client)"]
        T5["Case_Assignment (M:N junction)"]
    end

    FE -- "HTTPS + JWT (via Axios)" --> BE
    BE -- "pg driver" --> DB
```

---

## Evidence

### 1. Dashboard — Law Firm Overview

The landing page surfaces key operational metrics: total cases, in-progress vs closed split, estimated revenue, and a 6-month case growth chart rendered with ECharts. All figures come directly from live database queries (`/api/cases/monthly-stats`), so the dashboard always reflects the current state of the data.

LEXIS Dashboard

<img width="451" height="298" alt="image" src="https://github.com/user-attachments/assets/4af43a5c-3309-4326-91b7-1b5f133834ce" />


### 2. Case Management — sortable, filterable table

The Case Management view supports full-text search by case name, client, or lawyer; multi-column sorting; filter by status; and status/priority badges. Pagination handles 21+ seeded cases across multiple clients and lawyers. Every Edit action persists to PostgreSQL via the Express API — no client-side mocking.

LEXIS Case Management table

<img width="451" height="366" alt="image" src="https://github.com/user-attachments/assets/68c36389-eb52-474e-a2e0-5496f9765067" />


### 3. Code — CaseList component

The CaseList React component demonstrates the front-end patterns used throughout LEXIS: functional components, multiple `useState` Hooks for local state, `useEffect` for data loading, and authenticated Axios calls against the Express back-end with graceful error handling.

CaseList React component code

<img width="451" height="395" alt="image" src="https://github.com/user-attachments/assets/b899dae8-080a-4af6-ab47-880d4a17e706" />


Highlights from the implementation:

```jsx
export default function CaseList() {
    const [data, setData] = useState([]);
    const [clients, setClients] = useState([]);
    const [lawyers, setLawyers] = useState([]);
    const [loading, setLoading] = useState(false);
    const [searchText, setSearchText] = useState('');

    useEffect(() => {
        fetchCases();
        api.get('/api/clients').then((res) => setClients(res.data));
        api.get('/api/lawyers').then((res) => setLawyers(res.data));
    }, []);

    const fetchCases = () => {
        setLoading(true);
        api.get('/api/cases')
            .then((res) => setData(res.data))
            .catch(() => message.error('Failed to fetch cases from database.'))
            .finally(() => setLoading(false));
    };
    // ... rendering with Table, filters, search
}
```

### 4. API surface

REST endpoints exposed by the Express back-end:

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/test` | Health check |
| GET | `/api/cases` | List all cases (joined with client, lawyer, office) |
| POST | `/api/cases` | Create a new case with lawyer assignments |
| PUT | `/api/cases/:id` | Update a case and reassign lawyers (in a transaction) |
| DELETE | `/api/cases/:id` | Delete a case (cascades to assignments) |
| GET | `/api/clients` | List all clients |
| GET | `/api/lawyers` | List all lawyers with office info |
| GET | `/api/cases/monthly-stats` | Monthly new/closed case counts for the chart |

---

## Source code

**Repository:** [github.com/bellelynn/case-management](https://github.com/bellelynn/case-management)

The full LEXIS source code — React front-end (in `/client`), Express back-end (in `/server`), and the PostgreSQL schema + seed (in `/database/init.sql`) — is in this public repository. Anyone with the link can clone, set up the database, and run the project locally; full instructions are in the repository README.

The commit history shows iterative development across the semester, including the RBAC refactor (Goal 1) and the middleware additions (Goal 2).

---

## Challenges overcome

A few of the harder problems I worked through in LEXIS:

- **Many-to-many relationship handling.** Cases can have multiple lawyers assigned. Implementing create/update required database transactions to atomically insert into both the `Case` and `Case_Assignment` tables, with rollback on failure.
- **Keeping front-end and database in sync.** Ensuring that every create, update, and delete operation calls the real API and refreshes the table from the database, rather than just manipulating local state.
- **Responsive sidebar design.** Building a sidebar that shows full labels on desktop but collapses to icons on mobile, while maintaining active-link highlighting via React Router's `NavLink`.
- **Dashboard statistics accuracy.** Computing statistics (total, in-progress, closed, revenue) directly from live database query results, so the dashboard always reflects the current state of the data.

---

## What I learned (the hard way)

Moving from C# / Python to a JavaScript-everywhere stack was the hardest part of the semester. It was not just learning React — it was unlearning the synchronous, strongly-typed, batteries-included mindset that ASP.NET Core had given me.

The single worst offender was the **useEffect dependency array** — my instinct from C# component lifecycles was systematically wrong, and I burned several evenings debugging re-render loops before my mental model caught up.

The takeaway is bigger than React or Express: **real ecosystem fluency is not vocabulary — it is a rearrangement of how I think about state, time, and failure.** Whatever stack I learn next will need the same humility.

---

## Where this is going next

This semester delivered an integrated full-stack application on a market-relevant JavaScript ecosystem. The natural next steps, scoped in the Report's future goals section:

1. **Cloud deployment (carries forward PLP Goal 3 — Software Development Tools)** — LEXIS to AWS via Docker + GitHub Actions for CI/CD + ECS Fargate + RDS for PostgreSQL (next 3 months). Consolidates the deferred PLP Goal 3 (Jira, CI/CD, Docker, NUnit) into a single concrete deliverable.
2. **Application-layer security** — STRIDE threat model + OWASP WebGoat + `npm audit` / Dependabot in CI (next 4 months). Closes the loop on the original PLP Goal 2 scope.
3. **Responsible AI integration** — document-summarisation feature using the Anthropic or OpenAI API from the Express back-end, with cost logging and prompt-injection mitigations (next 6 months).
