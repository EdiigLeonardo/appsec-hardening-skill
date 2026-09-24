# Example conceptual finding

## [HIGH] Missing object-level authorization

**Status:** CONFIRMED
**Security domain:** Authorization
**Affected invariant:** User A must never access User B's private resource.
**Actor:** Authenticated user
**Resource:** Private resource
**Trust boundary:** Client -> trusted application boundary

**Evidence:**
The trusted component resolves a resource directly from a client-controlled identifier without constraining access using the authenticated subject or an authorization policy.

**Attack/abuse scenario:**
A user changes the identifier and retrieves a resource owned by another user.

**Impact:**
Cross-user data exposure.

**Recommendation:**
Enforce object-level authorization at the trusted execution boundary and derive subject identity from authenticated context.

**Verification:**
- owner -> own resource: allowed
- owner -> another user's resource: denied
- privileged actor -> resource: behavior matches explicit policy
