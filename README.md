# UTS 43030 — Professional Learning Portfolio

**Student:** Zhonghe Wang
**Student ID:** 25744879
**Subject:** 43030 Professional Practice in Computing — Autumn 2026
**Institution:** University of Technology Sydney

---

## About this portfolio

This portfolio collects the evidence behind my Assessment Task 3 Professional Learning Report. The structure mirrors my Professional Learning Plan (PLP). The PLP set out four learning goals; this Report reflects on the three I made measurable progress against this semester (Goals 1, 2, and 4) and carries the fourth (Software Development Tools) forward into the future goals in Section 6.

Some context on where I am coming from. My undergraduate background is a Bachelor of Laws, and I have spent the past two years deliberately retraining into software engineering. That trajectory shapes the portfolio — it is the reason Goal 1 (ethics and Indigenous data sovereignty) had unexpected depth for me, and the reason I have been so focused on the technical breadth (full-stack JavaScript, network-layer security) that the graduate ads at my target employers actually ask for.

**Target employers (from PLP):**
- **Entry-level goal:** Fire Front Solutions — Junior / Graduate Mobile App Developer
- **Mid-term goal:** Holocentric — Software Developer Fullstack (Permanent)

Both build mission-critical Australian software where privacy, security, and ethics decisions sit alongside delivery.

---

## Assessment documents

- [Professional Learning Plan (Assessment 1)](./43030_A1_25744879.pdf)
- [Professional Learning Report (Assessment 3)](./43030_A3_25744879.pdf)
---

## Three learning goals reflected on

### Goal 1 — Ethical principles and Indigenous data sovereignty
**Gap closed:** From a legalistic, checklist understanding of privacy to an architectural one.

[See evidence](./ethics-privacy/)

Key outputs: UTS Canvas modules on professional ethics and Indigenous data sovereignty; a privacy audit of the LEXIS PostgreSQL schema; an RBAC refactor that pushes access enforcement into the SQL queries themselves.

---

### Goal 2 — Foundational cybersecurity
**Gap closed:** From application-only OWASP knowledge to a working network-layer foundation.

[See evidence](./cybersecurity/)

Key outputs: Cisco Networking Academy Network Security course (most modules complete); defensive Express middleware added to the LEXIS back-end (body-size limits, content-type validation, JWT verification, rate limiting); a documented mid-semester rescope note explaining why the goal moved from application-layer OWASP to network-layer Cisco.

---

### Goal 4 — Full-stack development on the JavaScript ecosystem (LEXIS)
**Gap closed:** From a single-language C# / Python profile to genuine full-stack delivery on the JavaScript ecosystem.

[See evidence](./lexis/) | [Source code on GitHub](https://github.com/bellelynn/case-management)

Key outputs: **LEXIS**, a complete single-page Law Firm Case Management System. React 19 + Vite front-end with Ant Design 6 and ECharts. Node.js / Express 5 back-end with JWT auth. Normalised PostgreSQL schema (five tables). All four core workflows working: case listing, case detail with lawyer reassignment, client management, statistics dashboard.

---

## A note on PLP Goal 3 (Software Development Tools)

PLP Goal 3 committed me to learning Jira, CI/CD, Docker, and NUnit. I have made informal progress on Jira through university group projects but have nothing concrete to show for the rest, and LEXIS currently runs on localhost. Rather than claim partial completion here, I have carried this goal forward into Future Goal 1 in the Report — deploying LEXIS to AWS through a containerised CI/CD pipeline, which exercises all four of the original sub-skills in a single deliverable.

---

## Tech stack used this semester

**Front-end:** React 19, Vite, Ant Design 6, ECharts, React Router DOM v7, Axios
**Back-end:** Node.js, Express 5, JWT bearer-token auth, `pg` driver
**Database:** PostgreSQL (five normalised tables with FK constraints)
**Security:** Cisco Network Security (network layer), Express middleware (application layer), SQL-level RBAC
**Tooling:** Git, GitHub, VS Code, DBngin / Homebrew Postgres, Postman

---

## Contact

- **GitHub:** [@bellelynn](https://github.com/bellelynn)
- **University email:** zhonghe.wang@student.uts.edu.au

---

*This portfolio was created for UTS 43030 Assessment Task 3. Last updated: May 2026.*
