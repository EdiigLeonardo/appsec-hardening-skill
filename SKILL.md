# AppSec Hardening Skill

## Purpose

Perform repeatable defensive security hardening reviews based on security concepts, trust boundaries, data flows, and system behavior.

This skill MUST NOT depend on a specific programming language, framework, database, cloud provider, mobile stack, ORM, or deployment platform.

Technology-specific details may be used only as optional implementation context.

---

## Core principle

Review the system by asking:

1. What are the assets?
2. Who are the actors?
3. What are the trust boundaries?
4. What inputs are attacker-controlled?
5. What identities exist?
6. What actions can each identity perform?
7. What resources can each identity access?
8. What data crosses boundaries?
9. What invariants must never be violated?
10. What can be abused at scale, concurrently, or repeatedly?
11. What evidence exists that security controls are enforced?
12. What happens if one control fails?

Never treat a technology choice as a security control by itself.

---

## Invocation

Examples:

- `/appsec-hardening`
- `/appsec-hardening full`
- `/appsec-hardening diff`
- `/appsec-hardening release`
- `/appsec-hardening identity`
- `/appsec-hardening authorization`
- `/appsec-hardening data`
- `/appsec-hardening trust-boundaries`
- `/appsec-hardening abuse`
- `/appsec-hardening concurrency`
- `/appsec-hardening secrets`
- `/appsec-hardening observability`
- `/appsec-hardening supply-chain`
- `/appsec-hardening resilience`

Default mode: `full`.

---

## Modes

### full

Review the complete system across all conceptual domains.

### diff

Review only the changed attack surface.

Determine whether changes affect:

- trust boundaries;
- identity or session flows;
- authorization decisions;
- data exposure;
- resource ownership;
- state transitions;
- external calls;
- privileged operations;
- persistence;
- secrets;
- concurrency;
- asynchronous execution;
- file/content processing;
- deployment/security configuration;
- third-party dependencies.

### release

Perform a release gate review focused on failures that could cause:

- account takeover;
- authorization bypass;
- cross-user or cross-tenant access;
- arbitrary code or command execution;
- injection;
- sensitive data exposure;
- privilege escalation;
- secret compromise;
- unsafe state transitions;
- replay;
- double execution;
- large-scale abuse;
- compromise through dependencies or delivery pipeline.

---

## Security model discovery

Before reviewing controls, discover the system model.

Document:

### Assets

Examples:

- identities;
- credentials;
- sessions;
- personal data;
- financial data;
- business records;
- files;
- geolocation;
- cryptographic material;
- infrastructure credentials;
- administrative capabilities.

### Actors

Examples:

- anonymous user;
- authenticated user;
- privileged user;
- administrator;
- operator;
- service identity;
- background worker;
- external provider;
- attacker.

### Trust boundaries

Examples:

- client -> server;
- service -> service;
- application -> persistence;
- application -> external provider;
- queue -> worker;
- public network -> private network;
- user-controlled content -> parser;
- CI/CD -> production;
- tenant -> tenant.

### Entry points

Examples:

- HTTP/API;
- RPC;
- forms;
- file uploads;
- messages;
- webhooks;
- queues;
- deep links;
- imports;
- scheduled jobs;
- admin interfaces.

### Security invariants

Examples:

- User A must never read User B's private data.
- Tenant A must never affect Tenant B.
- A user must never grant themselves privileges.
- A price must never be taken from untrusted client input.
- A transaction must not execute twice.
- A resource must not transition to an invalid state.
- A secret must never reach an untrusted client.
- Administrative actions must be attributable.

---

## Evidence model

Every finding must be marked as:

- `CONFIRMED`
- `LIKELY`
- `NEEDS VERIFICATION`

Do not report a confirmed vulnerability without implementation evidence.

Whenever possible include:

- affected component;
- data flow;
- actor;
- trust boundary;
- resource;
- security invariant;
- code/config reference;
- reproduction logic;
- missing or failed control.

---

## Severity model

### CRITICAL

Direct or near-direct compromise with large blast radius.

Examples:

- identity bypass;
- arbitrary code execution;
- unrestricted privileged access;
- large-scale cross-tenant access;
- production secret compromise.

### HIGH

Serious security impact requiring limited exploitation conditions.

Examples:

- object-level authorization failure;
- privilege escalation;
- sensitive data disclosure;
- server-side request abuse into protected networks;
- persistent code/content injection;
- insecure recovery of privileged accounts.

### MEDIUM

Meaningful weakness with constrained blast radius or additional prerequisites.

### LOW

Defense-in-depth weakness with low direct exploitability.

### INFO

Security improvement, hygiene, or architecture recommendation.

---

# Conceptual review domains

## 1. Identity

Review:

- identity creation;
- authentication;
- federation;
- credential lifecycle;
- recovery;
- session creation;
- session refresh;
- logout;
- revocation;
- re-authentication;
- privileged identity.

Questions:

- How is identity established?
- What proves the identity?
- Can identity claims be forged or replayed?
- Can recovery bypass the primary authentication strength?
- Are sessions revocable?
- Does privilege elevation require stronger assurance?
- Can one identity create unlimited authentication attempts?

---

## 2. Authorization

Review:

- subject;
- action;
- resource;
- context;
- ownership;
- tenancy;
- role;
- permission;
- policy.

Security rule:

Authorization must be enforced at the trusted execution boundary.

Test conceptually:

- User A -> User B
- Tenant A -> Tenant B
- Normal user -> privileged action
- Service A -> Service B resource
- Read permission -> write operation
- Owner -> non-owner resource

Never trust authorization-relevant attributes supplied solely by an untrusted client.

---

## 3. Input and interpretation

All external or previously persisted attacker-controlled data is untrusted.

Review:

- type validation;
- range validation;
- structural validation;
- canonicalization;
- parser behavior;
- command construction;
- query construction;
- template rendering;
- deserialization;
- path handling;
- URL handling.

Look for transitions where data becomes code, query syntax, path syntax, markup, commands, or control flow.

---

## 4. Output and data exposure

Review:

- excessive fields;
- hidden/internal metadata;
- secrets;
- tokens;
- security decisions;
- personal data;
- cache behavior;
- error messages;
- logs.

Rule:

Return only the minimum information needed by the recipient.

---

## 5. State and workflow integrity

For each business workflow:

- identify allowed states;
- identify allowed transitions;
- identify actors allowed to transition;
- identify required preconditions;
- identify irreversible operations.

Test:

- skipped states;
- repeated actions;
- reversed actions;
- self-approval;
- forged status;
- out-of-order actions;
- duplicate actions.

---

## 6. Concurrency and atomicity

Identify flows with:

`read -> validate -> mutate`

Review for:

- race conditions;
- double spending;
- duplicate redemption;
- overbooking;
- quota bypass;
- replay;
- duplicate jobs.

Use appropriate concepts:

- atomic operations;
- transactions;
- uniqueness;
- locking;
- optimistic concurrency;
- idempotency.

---

## 7. Trust boundaries and isolation

At every boundary ask:

- Who controls the input?
- What identity crosses the boundary?
- What assumptions are made?
- Is authorization repeated?
- Is the message authentic?
- Can the sender impersonate another sender?
- Can the boundary be bypassed?

Review isolation between:

- users;
- tenants;
- services;
- environments;
- networks;
- privileged and non-privileged execution.

---

## 8. Secrets and key material

Review:

- creation;
- storage;
- distribution;
- access;
- rotation;
- revocation;
- logging;
- build-time exposure;
- client exposure.

Rule:

A secret available to an untrusted client is not a secret.

Prefer short-lived credentials and scoped capabilities.

---

## 9. External interactions

Review all outbound and inbound integrations.

Outbound:

- destination control;
- protocol control;
- redirects;
- private/internal destinations;
- response size;
- timeouts;
- trust of remote content.

Inbound:

- authenticity;
- integrity;
- replay resistance;
- ordering;
- idempotency;
- schema validation.

---

## 10. Content and file handling

Review:

- file type;
- content type;
- parser;
- size;
- compression ratio;
- metadata;
- filename/path;
- execution capability;
- active content;
- storage permissions;
- download authorization.

Treat uploaded content as hostile.

---

## 11. Resource abuse and availability

Review operations for attacker-controlled cost.

Examples:

- search;
- expensive computation;
- recursive structures;
- complex filters;
- graph traversal;
- geometric operations;
- report generation;
- bulk export;
- file processing.

Controls may include:

- quotas;
- rate limits;
- complexity limits;
- timeouts;
- size limits;
- budget limits;
- bounded pagination.

---

## 12. Data lifecycle and privacy

Review:

- collection;
- purpose;
- minimization;
- classification;
- retention;
- deletion;
- export;
- archival;
- backups;
- analytics copies;
- test/dev copies.

Ask whether the system stores more precision, history, or personal data than required.

---

## 13. Administrative and privileged operations

Review:

- elevated authentication;
- least privilege;
- separation of duties;
- approval workflows;
- bulk actions;
- impersonation;
- data export;
- destructive operations;
- auditability.

Privileged interfaces require a stricter threat model.

---

## 14. Observability and auditability

Security-relevant actions must be detectable and attributable.

Review logging for:

- identity events;
- authorization failures;
- privilege changes;
- sensitive exports;
- destructive actions;
- security configuration changes;
- anomalous use.

Never log secrets or credentials.

For important business actions, preserve an audit trail that supports reconstruction.

---

## 15. Error handling and failure modes

Review:

- fail-open behavior;
- exception handling;
- fallback behavior;
- partial failures;
- retry behavior;
- timeouts;
- dependency failures.

Security rule:

Authorization, validation, and integrity controls should fail closed where appropriate.

---

## 16. Cryptographic use

Review:

- purpose of cryptography;
- key management;
- random generation;
- nonce/IV handling;
- signing vs encryption;
- verification;
- expiration;
- algorithm agility.

Do not invent custom cryptographic protocols.

---

## 17. Supply chain

Review:

- third-party libraries;
- build tools;
- plugins;
- images;
- packages;
- actions;
- templates;
- external scripts;
- package integrity;
- lockfiles;
- provenance.

Assess what executes during build, install, test, and deploy.

---

## 18. Delivery pipeline

Review:

- source control protections;
- CI identities;
- secret access;
- artifact integrity;
- deployment approvals;
- environment separation;
- production access;
- rollback;
- release provenance.

Prefer short-lived workload identity over reusable credentials.

---

## 19. Environment isolation

Review:

- development;
- test;
- staging;
- production.

Check:

- credential separation;
- data separation;
- network separation;
- privilege separation;
- production data copied into lower environments;
- production access from developer machines.

---

## 20. Defense in depth

For critical security invariants, identify at least two independent controls where practical.

Example:

Application authorization + data-layer isolation.

Do not rely on multiple controls that all depend on the same untrusted claim.

---

# Abuse-case review

For every sensitive feature, ask:

- What if this is called 1,000 times?
- What if this is called concurrently?
- What if identifiers are changed?
- What if steps are skipped?
- What if values become negative?
- What if values are extremely large?
- What if the caller replays a valid request?
- What if a low-privileged user calls it directly?
- What if the external dependency lies?
- What if the same message is delivered twice?
- What if the request arrives out of order?
- What if the client modifies fields the UI never exposes?

---

# Differential review

For `diff` mode:

1. Identify changed components.
2. Determine which trust boundaries changed.
3. Determine which security invariants are affected.
4. Identify new inputs, outputs, identities, permissions, resources, and state transitions.
5. Review only relevant conceptual domains.
6. Expand scope if a change affects a shared security primitive.

---

# Output format

# AppSec Hardening Report

## Security model

- Assets:
- Actors:
- Trust boundaries:
- Entry points:
- Critical invariants:

## Executive summary

- Scope:
- Mode:
- Confirmed findings:
- Highest-risk domains:

## Findings

### [SEVERITY] Finding title

**Status:** CONFIRMED | LIKELY | NEEDS VERIFICATION

**Security domain:**

**Affected invariant:**

**Actor:**

**Resource:**

**Trust boundary:**

**Evidence:**

**Attack/abuse scenario:**

**Impact:**

**Recommendation:**

**Verification:**

---

## Developer ticket

For every CONFIRMED HIGH or CRITICAL finding:

### SECURITY-XXX — Title

**Priority:** P0 | P1 | P2 | P3

**Security invariant**

**Problem**

**Impact**

**Acceptance criteria**

- [ ] ...
- [ ] ...
- [ ] ...

**Regression tests**

- [ ] authorized path succeeds
- [ ] unauthorized path fails
- [ ] cross-identity access fails
- [ ] repeated execution is safe where applicable
- [ ] concurrent execution is safe where applicable

---

## Positive controls

Document verified security controls that are implemented correctly.

## Immediate priorities

Maximum 10.

## Hardening backlog

- P0 — Immediate
- P1 — High
- P2 — Medium
- P3 — Defense in depth

## Regression tests to add

## Requires manual review

---

# Technology adapters

Technology-specific adapters MAY exist, but:

- they are optional;
- they must not define the security model;
- they translate conceptual checks into implementation patterns;
- deleting all adapters must not make the skill unusable.

Example:

A database adapter may help locate query construction or row-level controls.

A framework adapter may help locate request handlers.

A mobile adapter may help locate secure storage.

The finding itself must still be expressed in concepts such as:

- broken authorization;
- untrusted input interpreted as executable/query syntax;
- secret exposure;
- weak identity assurance;
- missing replay protection;
- insufficient isolation.

---

# Final rule

Never ask:

"Is framework X configured securely?"

Ask:

"What security invariant must hold, where is it enforced, and what evidence proves that enforcement cannot be bypassed?"
