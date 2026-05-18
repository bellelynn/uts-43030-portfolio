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

![Working Ethically in the IT Industry — Canvas modules completed](./<img width="451" height="243" alt="image" src="https://github.com/user-attachments/assets/db0d2585-c94c-43dc-bb5b-a4d4b0389e3f" />
)

### 2. Canvas module completion — Indigenous Professional Capability

Completed all 8 sub-modules covering Reconciliation in Australia, Reconciliation Action Plans, Indigenous Data Sovereignty, Free Prior and Informed Consent, and the Case Study: An Ethical Dilemma.

![Indigenous Professional Capability — Canvas modules completed](./<img width="451" height="263" alt="image" src="https://github.com/user-attachments/assets/6478ca0f-740b-4800-ba7c-3e5ee5ce5630" />
)

### 3. LEXIS privacy audit

I classified every personal-data field in the LEXIS database into three categories, documenting the legal basis, retention period, and authorised role for each:

| Category | Examples | Legal basis | Retention | Authorised role |
|---|---|---|---|---|
| Client identifiers | name, email, phone, ABN | APP 3 (collection limited to what is reasonably necessary) | 7 years post-matter close | Lawyer, ClientManager |
| Matter details | case description, court documents, communications | APP 6 (use and disclosure) | 7 years post-matter close | Lawyer assigned to matter only |
| Billing data | time entries, invoices, payment records | APP 3 + ATO record-keeping requirement | 7 years (ATO) | BillingClerk, Lawyer |

**Audit findings:** the original LEXIS schema collected one field (client's preferred contact time) that was not used by any feature and was not necessary for the purpose of providing legal services. This violated the data minimisation principle of APP 3. **Action taken:** field removed from the schema; existing data dropped in the migration.

### 4. RBAC refactor

The original LEXIS design enforced access control only at the controller layer — meaning the repository would happily return any record asked of it, and a bug or future change in the controller could silently expose data. The refactored design pushes RBAC down to the repository, where it cannot be bypassed.

**Before:**
```csharp
// Controller-level check only
[Authorize(Roles = "Lawyer")]
public async Task<IActionResult> GetCase(int id) {
    var c = await _repo.GetByIdAsync(id);
    return Ok(c);
}

// Repository — no scope enforcement
public async Task<Case> GetByIdAsync(int id) {
    return await _db.Cases.FindAsync(id);
}
```

**After:**
```csharp
// Repository enforces user + role scope
public async Task<Case> GetByIdAsync(int id, ClaimsPrincipal user) {
    var userId = user.GetUserId();
    var c = await _db.Cases
        .Where(x => x.Id == id)
        .Where(x => x.AssignedLawyerId == userId 
                 || user.IsInRole("ClientManager"))
        .FirstOrDefaultAsync();
    if (c == null) throw new UnauthorizedAccessException();
    return c;
}
```

This pattern is now applied across every repository method in LEXIS.

---

## What changed in my thinking

Before this semester, I treated privacy as a checklist at the end of a project — write a notice, tick a box, move on. Auditing my own database forced me to see that every schema decision is already a privacy decision: what gets collected, how long it is kept, who can join what to what.

The Indigenous data sovereignty module pushed this further by challenging an assumption I had not noticed I was making — that data, once you have collected it, simply belongs to you. I now design data models by answering two questions up front: **who has authority over this data, and what is the minimum collection that does the job?**

---

## References

- Office of the Australian Information Commissioner. (2022). *Australian Privacy Principles guidelines.*
- Maiam nayri Wingara Indigenous Data Sovereignty Collective. (2018). *Indigenous data sovereignty communique.*
- Australian Computer Society. (2022). *ACS code of professional conduct.*
