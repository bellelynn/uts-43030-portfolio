# Goal 2 — Foundational Cybersecurity

[← Back to portfolio home](../)

## What I set out to do — and why it changed

**Original plan (in my PLP):** Work through the OWASP Top 10 to build application-layer security knowledge.

**What I actually did:** Switched to the Cisco Networking Academy's Network Security course in week 3 and stayed with it for the rest of the semester.

### Why I rescoped

Three weeks into the semester, I had two informational interviews with UTS alumni working in security-adjacent roles, and read more carefully through the Australian Cyber Security Centre's Essential Eight maturity model. Both pointed the same way: **without a network-layer foundation, an application-layer view of security would leave me unable to reason about the threat model my own code actually runs inside**.

Rescoping mid-semester was uncomfortable at the time, but I'd rather show honest progress against a revised plan than nominal completion of an unrealistic one. The full rescope note is below.

---

## Evidence

### 1. Cisco Networking Academy — enrolment

Enrolled in the Network Security course through UTS's Cisco Networking Academy partnership.

![Cisco Networking Academy — My Learning dashboard]
<img width="451" height="205" alt="image" src="https://github.com/user-attachments/assets/23015489-51aa-477b-9196-abb1732f7aff" />


### 2. Cisco Networking Academy — module-level completion

The Network Security course is structured around 12 modules covering secure network design, access control, firewall technologies, IPS, VPN, and common attack mitigations. The screenshot below shows the later modules (9–12) covering firewall technologies, zone-based policy firewalls, and IPS — most at 95–100% completion.

![Module-level completion: Firewalls and IPS]
<img width="211" height="316" alt="image" src="https://github.com/user-attachments/assets/20e28230-de59-49a3-88b2-b9072bb24e1a" />


**Summary by module:**

| Module | Topic | Completion |
|---|---|---|
| 1–4 | Network security fundamentals, threats, mitigations | ✅ Completed |
| 5–6 | Network device security, AAA, access control lists | ✅ Completed |
| 7–8 | Firewalls and firewall technologies | ✅ Completed |
| 9 | Firewall Technologies | 95% |
| 10 | Zone-Based Policy Firewalls | 95% |
| 11 | IPS Technologies | 97% |
| 12 | IPS Operation and Implementation | 100% |

**On the final certification exam:** I deliberately chose not to sit it before submitting this assessment. The practical lab content was worth doing properly, and partial completion with real understanding was a more honest outcome than a finished certificate I rushed through. I plan to sit the exam once I have completed the remaining lab work at the same depth.

### 3. Applied to LEXIS — defensive middleware

The course content has already changed how I write code. I now reason explicitly about which interfaces of the LEXIS API are network-exposed, and I added two pieces of defensive middleware to the back-end as a direct result.

**Input validation middleware** rejects malformed JSON, oversize payloads, and suspicious content-types:

```csharp
public class InputValidationMiddleware {
    private readonly RequestDelegate _next;
    private const int MaxBodySize = 1_000_000; // 1 MB

    public async Task InvokeAsync(HttpContext ctx) {
        if (ctx.Request.ContentLength > MaxBodySize) {
            ctx.Response.StatusCode = 413; // Payload Too Large
            return;
        }
        var contentType = ctx.Request.ContentType?.ToLower();
        if (ctx.Request.Method == "POST" 
            && !string.IsNullOrEmpty(contentType)
            && !contentType.StartsWith("application/json")) {
            ctx.Response.StatusCode = 415; // Unsupported Media Type
            return;
        }
        await _next(ctx);
    }
}
```

**Rate-limiting middleware** uses the .NET 7+ built-in rate limiter with a fixed-window strategy (100 requests / minute / IP):

```csharp
builder.Services.AddRateLimiter(opt => {
    opt.AddFixedWindowLimiter("api", limiter => {
        limiter.PermitLimit = 100;
        limiter.Window = TimeSpan.FromMinutes(1);
        limiter.QueueLimit = 10;
    });
});
app.UseRateLimiter();
```

### 4. Rescope note

**Date:** Week 3 of Autumn 2026  
**Original goal:** Application-layer security via OWASP Top 10  
**Revised goal:** Foundational network-layer security via Cisco Network Security

**Why I changed direction:**

1. Two informational interviews — one with a UTS alum working at a Sydney security consultancy, one with a UTS graduate now in cloud security at a major bank — both said the same thing: graduates who jump straight to application-layer security without understanding the network layer end up making confident mistakes about threat models.
2. The Australian Cyber Security Centre's Essential Eight framework is built around assumptions about network and host posture that I could not have evaluated without the Cisco material.
3. The OWASP Top 10 is still on my roadmap — it sits on top of, not instead of, the foundation. See Future Goal 2 in the Report.

**Cost of the change:** Roughly 8 hours sunk in the original OWASP plan were not directly reused, but the threat-modelling vocabulary carried over to the Cisco course.

---

## What changed in my thinking

**On security itself:** I used to think security was a layer you bolt on. After the Cisco course I think of it as a property of the system that has to be designed in at every layer simultaneously — and the network layer is where most of the assumptions other layers depend on actually live.

**On planning:** Rescoping cybersecurity, and deciding not to chase the certification at the cost of understanding, were both uncomfortable calls at the time. Looking back, making them — and being able to justify them on paper — is itself the kind of thing employers actually want. I expect to hit the same trade-off in industry, where renegotiating scope without losing trust is just part of the job.

---

## References

- Australian Cyber Security Centre. (2023). *Essential Eight maturity model.* Australian Signals Directorate.
- OWASP Foundation. (2021). *OWASP Top 10: 2021.*
