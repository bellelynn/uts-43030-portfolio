# Goal 1 — Ethics & Privacy in Software Engineering

[← Back to portfolio home](../)

## What I set out to do

Holocentric develops enterprise Risk and Compliance platforms for Australian government and regulated industries. Reading through their case studies and recent job ads made it clear that engineers there are expected to treat privacy as a first-class architectural concern, not a post-hoc legal review. My own situation was the opposite — I could quote the Australian Privacy Principles, but I had never let them change a database schema.

So I committed to two things in parallel:

1. Finishing the UTS Canvas modules on professional ethics, the IT code of ethics, and Indigenous data sovereignty
2. Using what I learned to audit the data model of LEXIS, my Law Firm Case Management System

Pairing the theory with an applied audit was deliberate — theory on its own would not have changed how I write code.

---

## Evidence

### 1. Canvas module completion — Working Ethically in the IT Industry

Completed all 8 sub-modules covering the IT code of ethics, privacy and data protection, intellectual property, cybersecurity and ethical hacking, emerging social implications, power and whistleblowers, and ethical thinking frameworks.

Working Ethically in the IT Industry — Canvas modules completed

<img width="451" height="243" alt="image" src="https://github.com/user-attachments/assets/371bed01-15a4-47a1-bd3e-39f151786c7d" />


### 2. Canvas module completion — Indigenous Professional Capability

Completed all 8 sub-modules covering Reconciliation in Australia, Reconciliation Action Plans, Indigenous Data Sovereignty, Free Prior and Informed Consent, and the Case Study: An Ethical Dilemma.

Indigenous Professional Capability — Canvas modules completed

<img width="451" height="263" alt="image" src="https://github.com/user-attachments/assets/a5da957c-9bb5-43a9-ad2e-bca6ef54bef5" />


### 3. LEXIS privacy audit

I classified every personal-data field in the LEXIS PostgreSQL schema into three categories, documenting the legal basis, retention period, and authorised role for each:

| Category | Examples | Legal basis | Retention | Authorised role |
|---|---|---|---|---|
| Client identifiers | name, email, phone, ABN | APP 3 (collection limited to what is reasonably necessary) | 7 years post-matter close | Lawyer, ClientManager |
| Matter details | case description, court documents, communications | APP 6 (use and disclosure) | 7 years post-matter close | Lawyer assigned to matter only |
| Billing data | time entries, invoices, payment records | APP 3 + ATO record-keeping requirement | 7 years (ATO) | BillingClerk, Lawyer |

**Audit findings:** the original LEXIS schema collected one field (client's preferred contact time) that was not used by any feature and was not necessary for the purpose of providing legal services. This violated the data minimisation principle of APP 3. **Action taken:** column dropped in a follow-up migration; existing data discarded.

### 4. RBAC pushed into the SQL layer

The original LEXIS design enforced access control only at the Express route handler — meaning the SQL query itself would happily return any record asked of it, and a bug or future change in the route layer could silently expose data. The refactored design pushes RBAC into the SQL query, where it cannot be bypassed even by a buggy route.

**Before:**
```javascript
// Route-handler check only
app.get('/api/cases/:id', authenticateJWT, async (req, res) => {
    if (req.user.role !== 'Lawyer') return res.status(403).end();
    const { rows } = await pool.query(
        'SELECT * FROM cases WHERE id = $1', [req.params.id]
    );
    res.json(rows[0]);
});
```

**After:**
```javascript
// Route handler is thin — query itself enforces scope
app.get('/api/cases/:id', authenticateJWT, async (req, res) => {
    const { rows } = await pool.query(`
        SELECT c.* 
        FROM cases c
        LEFT JOIN case_assignment ca ON ca.case_id = c.id
        WHERE c.id = $1
          AND (ca.lawyer_id = $2 OR $3 = 'ClientManager')
    `, [req.params.id, req.user.lawyerId, req.user.role]);
    if (!rows.length) return res.status(404).end();
    res.json(rows[0]);
});
```

This pattern — RBAC predicates baked into the query rather than gated by the route — is now applied across every read against client or case data in LEXIS.

---

## What changed in my thinking

Before this semester, I treated privacy as a checklist at the end of a project — write a notice, tick a box, move on. Auditing my own database forced me to see that every schema decision is already a privacy decision: what gets collected, how long it is kept, who can join what to what.

The Indigenous data sovereignty module pushed this further by challenging an assumption I had not noticed I was making — that data, once you have collected it, simply belongs to you. I now design data models by answering two questions up front: **who has authority over this data, and what is the minimum collection that does the job?**

---

## References

- Office of the Australian Information Commissioner. (2022). *Australian Privacy Principles guidelines.*
- Maiam nayri Wingara Indigenous Data Sovereignty Collective. (2018). *Indigenous data sovereignty communique.*
- Australian Computer Society. (2022). *ACS code of professional conduct.*
