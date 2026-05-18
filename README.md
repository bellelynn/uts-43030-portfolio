# UTS 43030 — Professional Learning Portfolio

**Student:** Zhonghe Wang  
**Student ID:** 25744879  
**Subject:** 43030 Professional Practice in Computing — Autumn 2026  
**Institution:** University of Technology Sydney

---

## About this portfolio

This portfolio collects the evidence behind my Assessment Task 3 Professional Learning Report. The structure mirrors my Professional Learning Plan: each of the three learning goals has its own section, and every claim made in the Report can be traced back here to a primary artefact — a screenshot, a code commit, or a document.

My target employers for this semester were **Holocentric** and **Fire Front** — two Australian companies building regulated software where engineers need to ship full-stack code and defend their privacy, security, and ethics decisions in the same week. The three goals below were each chosen to close a specific gap between my current capability and what those companies actually ask for in graduate job descriptions.

---

## Assessment documents

- 📄 [Professional Learning Plan (Assessment 1)](./docs/plp.pdf)
- 📄 [Professional Learning Report (Assessment 3)](./docs/report.pdf)

---

## Three learning goals

### 🎯 Goal 1 — Ethics & Privacy in Software Engineering
**Gap closed:** From a legalistic, checklist understanding of privacy to an architectural one.

[→ See evidence](./ethics-privacy/)

Key outputs: UTS Canvas modules on professional ethics and Indigenous data sovereignty; a privacy audit of the LEXIS PostgreSQL schema; an RBAC refactor that pushes access enforcement into the SQL queries themselves.

---

### 🔐 Goal 2 — Foundational Cybersecurity
**Gap closed:** From application-only OWASP knowledge to a working network-layer foundation.

[→ See evidence](./cybersecurity/)

Key outputs: Cisco Networking Academy Network Security course (most modules complete); defensive Express middleware added to the LEXIS back-end (body-size limits, content-type validation, JWT verification, rate limiting); a documented mid-semester rescope note explaining why the goal moved from application-layer to network-layer.

---

### ⚛️ Goal 3 — Full-Stack Development on the JavaScript Ecosystem (LEXIS)
**Gap closed:** From a single-language C# profile to genuine full-stack delivery on the JavaScript ecosystem.

[→ See evidence](./lexis/) | [→ Source code on GitHub](https://github.com/bellelynn/case-management)

Key outputs: **LEXIS**, a complete single-page Law Firm Case Management System. React 19 + Vite front-end with Ant Design 6 and ECharts. Node.js / Express 5 back-end with JWT auth. Normalised PostgreSQL schema (five tables). All four core workflows working: case listing, case detail with lawyer reassignment, client management, statistics dashboard.

---

## Tech stack used this semester

**Front-end:** React 19, Vite, Ant Design 6, ECharts, React Router DOM v7, Axios  
**Back-end:** Node.js, Express 5, JWT bearer-token auth, `pg` driver  
**Database:** PostgreSQL (five normalised tables with FK constraints)  
**Security:** Cisco Network Security (network layer), Express middleware (application layer), SQL-level RBAC  
**Tooling:** Git, GitHub, VS Code, DBngin (local Postgres), Postman

---

## Contact

- **GitHub:** [@bellelynn](https://github.com/bellelynn)
- **University email:** zhonghe.wang@student.uts.edu.au

---

*This portfolio was created for UTS 43030 Assessment Task 3. Last updated: May 2026.*
