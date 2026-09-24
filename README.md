# AppSec Hardening Skill — Conceptual Edition

[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](manifest.json)
[![Architecture](https://img.shields.io/badge/architecture-technology--agnostic-success.svg)](manifest.json)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

> **A technology-agnostic Application Security (AppSec) hardening skill and review playbook for AI coding assistants and security auditors.**

Unlike traditional security checklists that depend on specific syntax or framework linters, **AppSec Hardening (Conceptual Edition)** evaluates systems against fundamental security invariants, trust boundaries, state transitions, data flows, and failure modes.

---

## Table of Contents

- [Overview & Philosophy](#-overview--philosophy)
- [Key Features](#-key-features)
- [Architecture & Core Domains](#-architecture--core-domains)
- [Installation & Setup](#-installation--setup)
  - [Antigravity IDE & Gemini CLI](#1-antigravity-ide--gemini-cli)
  - [Claude Code](#2-claude-code)
  - [Cursor / Cline / Roo Code / OpenCode](#3-cursor--cline--roo-code--opencode)
  - [Standalone Git Integration](#4-standalone-git-integration)
- [Usage & Commands](#-usage--commands)
  - [Invocation Syntax](#invocation-syntax)
  - [Review Modes](#review-modes)
  - [Targeted Domain Reviews](#targeted-domain-reviews)
- [Repository Structure](#-repository-structure)
- [Reporting & Templates](#-reporting--templates)
- [Extensibility & Technology Adapters](#-extensibility--technology-adapters)
- [Contributing](#-contributing)
- [License](#-license)

---

## Overview & Philosophy

Most application vulnerabilities arise from flawed assumptions at boundaries, not missing framework syntax. This skill does **not** ask:

> "Is framework X configured with option Y?"

Instead, it asks:

> "Which security invariant must hold, where is the trust boundary enforced, and what verifiable evidence proves that it cannot be bypassed?"

### What Changed in v2.0.0

This edition makes **no assumptions** regarding:
- ❌ Programming language (Python, TypeScript, Go, Rust, Java, C#, etc.)
- ❌ Web or Mobile stack (React, Next.js, Vue, Flutter, iOS Swift, Android Kotlin)
- ❌ Database or Storage (SQL, NoSQL, Key-Value, Object Storage)
- ❌ Cloud Provider or Runtime (AWS, GCP, Azure, bare metal, Docker/Kubernetes)
- ❌ Specific CI/CD vendors

Technology-specific nuances are isolated into optional [Technology Adapters](adapters/README.md) without polluting the core invariant verification engine.

---

## Key Features

- **Invariant-Centric Review**: Pinpoints missing authorization, broken state machines, unsafe parsing, race conditions, and privilege escalations.
- **Diff & Release Gate Modes**: Run lightning-fast incremental reviews on pull requests (`diff`) or rigorous pre-deployment checks (`release`).
- **Defensive Paradigms Built-In**: Enforces Zero Trust, Least Privilege, Deny-by-Default, Assume Breach, Defense in Depth, and Secure-by-Default principles.
- **Evidence-Based Findings**: Produces structured findings with explicit attack scenarios, impacted invariants, severity ratings, and reproducible verification steps.

---

## Architecture & Core Domains

The skill evaluates code across **14 core conceptual domains** and **6 defensive paradigms**:

### 14 Conceptual Domains (`concepts/`)

| Domain | Focus & Invariants |
| :--- | :--- |
| **`identity`** | Subject authenticity, session lifecycles, non-repudiation, token invalidation. |
| **`authorization`** | Object-level / function-level access control, tenant boundaries, least privilege. |
| **`trust-boundaries`** | Untrusted input demarcation, execution domain transitions, perimeter validation. |
| **`input-interpretation`** | Semantic validation, canonicalization, injection resistance, safe parsing. |
| **`state-integrity`** | State machine transitions, monotonic counters, replay/duplicate execution prevention. |
| **`concurrency`** | Race conditions, TOCTOU, double-spend, distributed locks, idempotency. |
| **`secrets`** | Safe material storage, zero-leakage via logs/exceptions, ephemeral credentials. |
| **`external-interactions`** | SSRF prevention, webhook verification, untrusted upstream timeout/fail-safes. |
| **`content-files`** | MIME spoofing, zip-bombs/path traversal, safe upload handling & sandboxed storage. |
| **`availability-abuse`** | Rate limiting, expensive compute exhaustion, algorithmic complexity attacks. |
| **`data-lifecycle`** | Retention, secure erasure, encryption-at-rest/in-transit, PII containment. |
| **`observability`** | Audit logging of security events, tamper-evident traces, zero sensitive data logging. |
| **`supply-chain`** | Dependency pinning, integrity checks (lockfiles/hashes), minimal attack surface. |
| **`delivery-pipeline`** | Build immutability, pipeline isolation, least-privilege deployment tokens. |

### 6 Defensive Paradigms (`paradigms/`)

1. **Zero Trust** — Never trust, always verify every request and boundary.
2. **Least Privilege** — Minimal required capability per actor, role, or service.
3. **Deny-by-Default** — Explicit allowlists only; fallback is always denial.
4. **Assume Breach** — Blast-radius containment and defense against subverted dependencies.
5. **Defense in Depth** — Multi-layered controls; no single point of security failure.
6. **Secure-by-Default** — Safe failure modes, hardened defaults, opt-in risk.

---

## Installation & Setup

You can install this skill globally or per-workspace in any AI assistant supporting prompt-based skills, rules, or workflows.

### 1. Antigravity IDE & Gemini CLI

#### Workspace Installation (Current Project Only)
Clone or copy into the workspace customization directory:

```bash
mkdir -p .agents/skills
git clone https://github.com/EdiigLeonardo/appsec-hardening-skill.git .agents/skills/appsec-hardening
```

#### Global Installation (Available Across All Projects)
```bash
mkdir -p ~/.gemini/config/skills
git clone https://github.com/EdiigLeonardo/appsec-hardening-skill.git ~/.gemini/config/skills/appsec-hardening
```

---

### 2. Claude Code

Install as a custom skill / playbook for Claude Code:

```bash
mkdir -p .claude/skills
git clone https://github.com/EdiigLeonardo/appsec-hardening-skill.git .claude/skills/appsec-hardening
```

Or add a reference to `SKILL.md` inside your `.claude/config.json` or system instructions.

---

### 3. Cursor / Cline / Roo Code / OpenCode

To use with Cursor or extension-based agents:

1. Clone the repository into your preferred shared directory or project folder:
   ```bash
   git clone https://github.com/EdiigLeonardo/appsec-hardening-skill.git
   ```
2. Symlink or include `SKILL.md` in your `.cursorrules` or `.cursor/rules/appsec-hardening.mdc`:
   ```bash
   mkdir -p .cursor/rules
   ln -s /path/to/appsec-hardening-skill/SKILL.md .cursor/rules/appsec-hardening.mdc
   ```

---

### 4. Standalone Git Integration

Clone directly to your local tools repository:

```bash
git clone https://github.com/EdiigLeonardo/appsec-hardening-skill.git ~/skills/appsec-hardening
```

---

## Usage & Commands

### Invocation Syntax

Trigger the skill in your AI assistant or agent chat using the slash command or prompt prefix:

```text
/appsec-hardening [mode | domain]
```

### Review Modes

| Command | Mode | Description |
| :--- | :--- | :--- |
| `/appsec-hardening` | `full` | Complete architecture and codebase defensive review across all 14 domains. |
| `/appsec-hardening full` | `full` | Explicit full-system threat & invariant audit. |
| `/appsec-hardening diff` | `diff` | Incremental review targeting only modified code and changed attack surface (PR review). |
| `/appsec-hardening release` | `release` | Release-gate audit focusing on critical failure modes (account takeover, RCE, IDOR, leakages). |

### Targeted Domain Reviews

Run focused audits on specific security areas:

```text
/appsec-hardening identity          # Session, authentication, token lifecycles
/appsec-hardening authorization     # Object-level access control (IDOR/BOLA), tenant isolation
/appsec-hardening concurrency       # Race conditions, atomic operations, distributed locks
/appsec-hardening abuse             # Rate limiting, resource exhaustion, denial of service
/appsec-hardening secrets           # Credential exposure, memory hygiene, key management
/appsec-hardening trust-boundaries  # Parameter tampering, boundary crossing, RPC/API isolation
/appsec-hardening supply-chain      # Third-party dependency security & integrity
```

---

## Repository Structure

```
appsec-hardening-skill/
├── manifest.json              # Skill metadata and core domain declarations
├── SKILL.md                   # Compact workflow and selective reference routing
├── LICENSE                    # MIT License
├── README.md                  # Project documentation & user guide
├── concepts/                  # 14 Domain-specific security guidelines
│   ├── authorization.md
│   ├── availability-abuse.md
│   ├── concurrency.md
│   ├── content-files.md
│   ├── data-lifecycle.md
│   ├── delivery-pipeline.md
│   ├── external-interactions.md
│   ├── identity.md
│   ├── input-interpretation.md
│   ├── observability.md
│   ├── secrets.md
│   ├── state-integrity.md
│   ├── supply-chain.md
│   └── trust-boundaries.md
├── paradigms/                 # 6 Core defensive security paradigms
│   ├── assume-breach.md
│   ├── defense-in-depth.md
│   ├── deny-by-default.md
│   ├── least-privilege.md
│   ├── secure-by-default.md
│   └── zero-trust.md
├── templates/                 # Standardized reporting & audit templates
│   ├── developer-ticket.md    # Confirmed HIGH/CRITICAL remediation ticket
│   ├── finding.md             # Single vulnerability / finding format
│   ├── report.md              # Full audit report structure
│   └── security-model.md      # Threat model & invariant specification template
├── adapters/                  # (Optional) Technology/Stack-specific helpers
│   └── README.md
└── examples/                  # Reference examples
    └── conceptual-finding.md  # Sample structured finding
```

---

## Context efficiency

`SKILL.md` contains the shared workflow and routes to existing references. Full reviews cover all 14 domains, including the 20 original review topics; focused and diff reviews load only affected domains and expand when evidence implicates shared controls. Templates are read when their output is needed. README, examples, paradigms, and adapters are not loaded by default.

Reports retain evidence, confidence, severity, coverage, and manual-review gaps. Findings and developer tickets cross-reference evidence rather than repeating it. Token savings depend on the tokenizer, review scope, and inspected code; shorter instructions do not replace investigation.

## Reporting & Templates

Every finding generated by the skill adheres to the structured format in [`templates/finding.md`](templates/finding.md):

```markdown
## [HIGH] Missing object-level authorization

**Status:** CONFIRMED
**Security domain:** Authorization
**Affected invariant:** User A must never access User B's private resource.
**Actor:** Authenticated user
**Resource:** Private invoice document
**Trust boundary:** Client -> API Gateway -> Service Boundary

**Evidence:**
The invoice handler queries the repository using `invoiceId` supplied in route parameters without asserting that `invoice.ownerId == request.auth.userId`.

**Attack/abuse scenario:**
An attacker changes `invoiceId` in the request URL to inspect arbitrary customer invoices.

**Impact:**
Cross-tenant confidentiality breach and PII disclosure.

**Recommendation:**
Enforce object-level ownership check at the data access layer: `WHERE id = :invoiceId AND owner_id = :userId`.

**Verification:**
- User A -> User A Invoice: Allowed (200 OK)
- User A -> User B Invoice: Denied (403 Forbidden / 404 Not Found)
```

---

## Extensibility & Technology Adapters

Adapters provide language- or framework-specific translation notes (e.g., how concurrency primitives look in Go vs. Python AsyncIO vs. Java).

- Adapters are located in the [`adapters/`](adapters/) directory.
- Adapters **never** redefine the conceptual security model; they merely illustrate common framework implementations.
- The core skill remains completely operational even if `adapters/` is empty or omitted.

---

## Contributing

Contributions are welcome! You can contribute by:
1. Refining invariant definitions in [`concepts/`](concepts/).
2. Adding edge-case scenarios or defensive paradigms in [`paradigms/`](paradigms/).
3. Submitting optional technology adapters in [`adapters/`](adapters/).
4. Improving documentation and examples.

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.
