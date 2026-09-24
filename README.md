# appsec-hardening — conceptual edition

A technology-agnostic AppSec hardening skill based on security concepts and paradigms.

## What changed

This version does NOT assume:

- a frontend framework;
- a backend framework;
- a programming language;
- SQL or NoSQL;
- mobile or web;
- a cloud provider;
- containers;
- a specific CI/CD platform.

Instead it reviews:

- assets;
- actors;
- trust boundaries;
- identity;
- authorization;
- input interpretation;
- state integrity;
- concurrency;
- secrets;
- isolation;
- external interactions;
- resource abuse;
- data lifecycle;
- observability;
- supply chain;
- delivery pipeline;
- defense in depth.

Technology-specific adapters are optional and can be replaced without changing the core skill.

## Recommended invocation

```text
/appsec-hardening full
/appsec-hardening diff
/appsec-hardening release
/appsec-hardening authorization
/appsec-hardening identity
/appsec-hardening abuse
/appsec-hardening concurrency
```

## Design rule

The skill should not ask:

> Is technology X configured correctly?

It should ask:

> Which security invariant must hold, where is it enforced, and what evidence proves that it cannot be bypassed?
