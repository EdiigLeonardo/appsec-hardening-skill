# Authorization

Model subject + action + resource + context. Require explicit policy permission for every action.

Review:

subject; action; resource; context; ownership; tenancy; role; permission; policy.

Security rule:

Authorization must be enforced at the trusted execution boundary.

Test conceptually:

User A -> User B; Tenant A -> Tenant B; Normal user -> privileged action; Service A -> Service B resource; Read permission -> write operation; Owner -> non-owner resource.

Never trust authorization-relevant attributes supplied solely by an untrusted client.

## Administrative and privileged operations

Review:

elevated authentication; least privilege; separation of duties; approval workflows; bulk actions; impersonation; data export; destructive operations; auditability.

Privileged interfaces require a stricter threat model.
