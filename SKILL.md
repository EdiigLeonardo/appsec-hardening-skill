---
name: appsec-hardening
description: Review application security through trust boundaries, data flows, and security invariants. Use for defensive full-system, diff, release-gate, or focused domain reviews.
---

# AppSec Hardening

Technology-agnostic defensive review. A technology choice is not evidence of a security control. Identify the invariant, its enforcement point, and evidence that it cannot be bypassed.

## Scope and context budget

Invocation: `/appsec-hardening [full|diff|release|domain]`; if no mode is specified, the default is `full`, but always follow the invoked mode when specified.

- `full`: assess every domain below across the system. Mark absent surfaces not applicable with a reason; do not silently skip domains.
- `diff`: inspect the diff, affected boundaries/invariants, and new inputs, outputs, identities, permissions, resources, state transitions, async work, persistence, configuration, and dependencies. Follow relevant callers and enforcement paths; expand to consumers when a shared security primitive changes.
- `release`: prioritize account takeover, authorization bypass, cross-user/tenant access, code/command execution, injection, data/secret exposure, privilege escalation, unsafe transitions, replay, double execution, large-scale abuse, and dependency/pipeline compromise.
- `domain`: focus on that domain and connected enforcement paths. Aliases: `data` → data-lifecycle; `abuse` → availability-abuse; `resilience` → availability-abuse + external-interactions + concurrency.

Search existing security helpers, policies, tests, and project documentation before tracing implementations. Use bounded searches and relevant file ranges; reuse evidence rather than rereading files. Read each selected reference once, not the entire skill directory. Do not load README, examples, paradigms, or adapters by default. Context economy must not limit evidence gathering or omit relevant controls.

## Review

1. Discover assets, actors/identities, entry points, attacker-controlled inputs, trust boundaries, data flows, privileges, sensitive data, external dependencies, and critical invariants. Use [security-model](templates/security-model.md) when documenting the model; keep unknowns explicit.
2. Select references using the table:
   - In `full`, cover every row.
   - In other modes, load the rows implicated by the scope and follow evidence into related domains.
   - For each selected row, perform the additional checks named alongside it.
3. Trace sensitive flows from entry to enforcement and effects. Check authorized and unauthorized paths, cross-identity/tenant access, direct low-privilege calls, hidden client fields, modified identifiers, negative/extreme values, skipped/reversed steps, repetition, concurrency, replay, duplicate/out-of-order messages, dishonest dependencies, and scale (e.g. 1,000 calls).
4. Assess default denial, least privilege, explicit trust verification, secure defaults, compromise containment, and failure behavior. For critical invariants seek at least two independent controls where practical, not controls sharing an untrusted claim. See [defense-in-depth](paradigms/defense-in-depth.md) only for additional guidance.
5. Report evidence, gaps, and verified positive controls. Recommend minimal fixes using existing enforcement patterns and meaningful regression checks.

| Domain/reference                                           | Coverage                                                                                |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| [identity](concepts/identity.md)                           | Proof, credentials, recovery, sessions, revocation, elevation                           |
| [authorization](concepts/authorization.md)                 | Subject/action/resource/context, ownership, tenancy, privileged/admin operations        |
| [input-interpretation](concepts/input-interpretation.md)   | Validation, canonicalization, data becoming executable syntax                           |
| [data-lifecycle](concepts/data-lifecycle.md)               | Output exposure, privacy, collection through deletion, copies/caches                    |
| [state-integrity](concepts/state-integrity.md)             | States, actors, preconditions, irreversible operations                                  |
| [concurrency](concepts/concurrency.md)                     | Read-check-write, atomicity, replay, idempotency                                        |
| [trust-boundaries](concepts/trust-boundaries.md)           | Sender authenticity, bypasses, user/service/network/environment isolation               |
| [secrets](concepts/secrets.md)                             | Credential lifecycle/exposure, cryptography, keys, nonces, verification                 |
| [external-interactions](concepts/external-interactions.md) | Inbound/outbound trust, error handling, partial failures, retries, fail-closed controls |
| [content-files](concepts/content-files.md)                 | Hostile content, parsers, paths, decompression, download authorization                  |
| [availability-abuse](concepts/availability-abuse.md)       | Attacker-controlled cost, quotas, complexity and resource bounds                        |
| [observability](concepts/observability.md)                 | Attributable, reconstructable security events; never log secrets                        |
| [supply-chain](concepts/supply-chain.md)                   | Dependencies, integrity, provenance, install/build/test/deploy execution                |
| [delivery-pipeline](concepts/delivery-pipeline.md)         | Source-to-production integrity, identities, approvals, rollback                         |

## Evidence and severity

Every finding: `CONFIRMED` (implementation evidence demonstrates the failure), `LIKELY` (supported inference with an unresolved condition), or `NEEDS VERIFICATION` (insufficient evidence). Never confirm from a missing search match or technology choice alone. Distinguish untested controls from failed controls.

Rate impact, prerequisites, and blast radius independently of confidence:

- `CRITICAL`: direct/near-direct large-scale compromise (identity bypass, arbitrary execution, unrestricted privilege, broad tenant access, production secrets).
- `HIGH`: serious impact with limited prerequisites (object authorization, escalation, sensitive disclosure, protected-network request abuse, persistent injection, privileged recovery).
- `MEDIUM`: meaningful impact with constrained scope or additional prerequisites.
- `LOW`: low-exploitability defense-in-depth weakness.
- `INFO`: hygiene or architectural improvement.

## Output

Read [report](templates/report.md) for the report structure and [finding](templates/finding.md) only when findings exist. Use [developer-ticket](templates/developer-ticket.md) for every `CONFIRMED` `HIGH`/`CRITICAL` finding. Keep evidence and reproduction details sufficient to verify the claim; never paste secrets.

Be concise: one finding per root cause, cite affected locations/flows, and cross-reference findings in tickets and priorities instead of repeating evidence. Omit empty finding/ticket blocks; state when no findings were confirmed and what remains unverified. Scope, coverage, limitations, and manual-review gaps must remain visible. Keep immediate priorities to at most 10.

Technology adapters are optional implementation locators, never the security model. Read only a relevant adapter if present; the skill must work without adapters. Express findings in security concepts regardless of stack.
