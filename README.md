# AI-Assisted Development — Master Prompt Library
> **Workflow:** Your `/documents/` folder is the source of truth. Blueprint.md is written by you manually before running any prompt. Every subsequent prompt reads existing files from `/documents/` and writes a new file there. Run prompts in order — each document validates against everything already written.

---

## ⚠️ Pre-Flight: Write Blueprint.md First

Before running any prompt, manually create `/documents/Blueprint.md` using this structure. The AI does not generate this — **you write it**. It is your thinking, not the AI's.

```markdown
# Blueprint — [Project Name]
_Last updated: [date] | Status: [DRAFT / LOCKED]_

## 1. Project Identity
- **Name:**
- **One-line purpose:**
- **Core problem being solved:**
- **Why this problem, why now:**

## 2. Users and Roles
For each role:
- **Role name:**
- **Who they are:**
- **Primary goal:**
- **Current frustration:**

## 3. Platform and Tech Stack
- **Client:** [e.g. Android (Kotlin + Jetpack Compose), min SDK 26]
- **Backend:** [e.g. Node.js + Express / Firebase / Supabase]
- **Database:** [e.g. PostgreSQL / Firestore / SQLite]
- **Auth:** [e.g. Firebase Auth / custom JWT]
- **Storage:** [e.g. Firebase Storage / S3]
- **Stack justification:** [Why these choices for this project]

## 4. Core Features (by role)
List capabilities, not implementation. No UI details here.

## 5. Non-Functional Requirements
- Performance:
- Security:
- Availability:
- Offline support:

## 6. Explicit Out-of-Scope
[Be ruthless. Every ambiguous feature that is NOT listed here will be built.]

## 7. Constraints
- Team size:
- Timeline:
- Budget:
- Compliance:

## 8. Success Metrics
[How do you know v1.0 worked?]
```

> Once Blueprint.md is locked, do not edit it without also re-running every downstream prompt that depends on it.

---

## 00 · Blueprint Review Gate

> **Run this before generating any other document. It is a stress test, not a generation prompt.**

```
Read /documents/Blueprint.md carefully.

Do not generate any new document. Your only job is to find problems with what is already written.

Audit the Blueprint for the following and report findings only — do not fix them:

1. **Scope gaps** — Are there user roles mentioned without corresponding features? Are there features mentioned without a clear owning role?

2. **Internal contradictions** — Does any statement in one section conflict with a statement in another?

3. **Dangerous vagueness** — Identify every phrase that a developer could interpret in two or more incompatible ways. Quote the phrase and explain the ambiguity.

4. **Missing constraints** — What decisions have been left open that will block document generation downstream? Specifically: auth mechanism, offline strategy, data ownership rules, third-party service choices.

5. **Scope creep risks** — Which features are broad enough that a developer could over-build them? Flag with an estimated complexity multiplier.

6. **Out-of-scope gaps** — What obvious related features are NOT in the out-of-scope list that a developer might reasonably assume are included?

Format your output as:
## ⚠️ MUST RESOLVE BEFORE PROCEEDING
[Blockers — document generation will produce unreliable output if these are not fixed]

## ⚡ SHOULD RESOLVE
[Non-blockers that will cause inconsistencies downstream]

## 💬 OPEN QUESTIONS
[Decisions not yet made that will need to be made during generation anyway]

Do not suggest fixes. The human resolves these, then relocks Blueprint.md.
```

---

## 01 · PRD.md

```
Read /documents/Blueprint.md.

Your job is to produce a Product Requirements Document and write it to /documents/PRD.md.

VALIDATION BEFORE WRITING:
Before generating content, verify:
- Every role in Blueprint.md has at least one feature in this PRD
- Every feature in this PRD traces to a statement in Blueprint.md — if it does not, do not include it
- The MVP scope does not include anything listed as out-of-scope in Blueprint.md

If you find a contradiction between what the PRD logically requires and what Blueprint.md says, stop and output:
⚠️ CONFLICT: [describe the conflict]
Do not resolve it. Do not continue past a CONFLICT until instructed.

DOCUMENT STRUCTURE:
1. Executive Summary — problem, solution, why now (derived from Blueprint, not rewritten in marketing language)

2. User Personas — for each role from Blueprint.md:
   - Who they are and their job-to-be-done
   - Pain points with current solutions
   - Success criteria: what does "this worked" look like for them

3. Feature Specifications — for each feature:
   - Feature ID: F-[MODULE]-[NUMBER]
   - Name and one-line description
   - User story: "As a [role], I want to [action] so that [outcome]"
   - Acceptance criteria: numbered, testable, unambiguous — each criterion must be verifiable by a QA engineer without asking clarifying questions
   - Priority: P0 (MVP blocker) | P1 (MVP important) | P2 (post-MVP)
   - Blueprint reference: [quote the exact Blueprint.md line this derives from]
   - Dependencies: [other Feature IDs this requires]

4. MVP Scope — explicit list of Feature IDs shipping in v1.0 vs deferred

5. Non-Functional Requirements — with measurable targets, not adjectives ("< 2s load time" not "fast")

6. Assumptions and Risks — what must be true for this PRD to be valid; what kills it if wrong

7. Open Questions — unresolved decisions, each tagged [OWNER] and [DEADLINE]

OUTPUT: Write the complete document to /documents/PRD.md
```

---

## 02 · Technical_Requirements.md

```
Read /documents/Blueprint.md and /documents/PRD.md.

VALIDATION BEFORE WRITING:
- Confirm the tech stack you are about to specify matches Blueprint.md Section 3 exactly
- Flag any PRD feature that implies a technology not listed in Blueprint.md:
  ⚠️ TECH GAP: Feature [F-ID] requires [technology/capability] which is not in Blueprint.md stack

Do not add technologies to resolve gaps silently. Flag them and wait.

DOCUMENT STRUCTURE:
Write /documents/Technical_Requirements.md covering:

1. Platform and Runtime
   - Target platforms with exact version minimums
   - Runtime environment specifications

2. Technology Stack — every layer with version numbers and justification tied to a specific Blueprint constraint (not generic best practice)

3. Third-Party Integrations
   - Each service: purpose, version, SDK, fallback if unavailable
   - Version pinning policy

4. Development Standards
   - Language version
   - Code style guide (link or inline rules — not "follow community standards")
   - Folder and module structure with example tree
   - Naming conventions: files, classes, functions, variables, DB tables — one rule per category
   - Git: branching model, branch naming, commit message format (with example)
   - PR requirements: review count, CI must pass, checklist items

5. Build and Deployment
   - Local setup: exact steps a new developer runs on day one
   - Build pipeline stages in order
   - Environments: what differs between dev / staging / prod (config, data, services)
   - Release versioning scheme

6. Performance Requirements — measurable SLAs per the NFRs in PRD.md

7. Dependency Policy — criteria for adding a dependency, vulnerability scan cadence

8. Hard Constraints — things the team must never do, with reason

OUTPUT: Write to /documents/Technical_Requirements.md
```

---

## 03 · Security_Requirements.md

```
Read /documents/Blueprint.md, /documents/PRD.md, and /documents/Technical_Requirements.md.

VALIDATION BEFORE WRITING:
- Every user role in Blueprint.md must appear in the threat model
- Every data type mentioned in PRD.md must have a classification
- If Technical_Requirements.md specifies a technology with known security implications (e.g. SQLite, WebView, JWT), address it explicitly — do not skip it because it seems obvious

DOCUMENT STRUCTURE:
Write /documents/Security_Requirements.md covering:

1. Threat Model
   - Adversary list: who attacks this system and what do they want
   - High-value targets: ranked by impact if compromised
   - STRIDE table: for each major component (auth service, API, database, mobile client, admin portal):
     | Component | S | T | R | I | D | E | Risk Rating |
   - Risk rating: CRITICAL / HIGH / MEDIUM / LOW with one-sentence justification

2. Authentication and Session Management
   - Mechanism and token lifecycle (access + refresh)
   - Expiry windows per role
   - Device binding strategy
   - MFA requirements per role: REQUIRED / OPTIONAL / NOT APPLICABLE

3. Authorisation
   - Server-side enforcement requirement (client-side is presentation only, never authorisation)
   - Behaviour on escalation attempt: log, alert, terminate session

4. Data Security
   - Classification table: every data type → PUBLIC / INTERNAL / CONFIDENTIAL / RESTRICTED
   - Encryption at rest: field-level where RESTRICTED, table-level where CONFIDENTIAL
   - Encryption in transit: TLS minimums, certificate pinning decision for mobile
   - PII fields: list, retention period, deletion procedure

5. Input Validation
   - Validation rules by input type (string, integer, email, file upload, etc.)
   - Attack surface: every user-controlled input in the system

6. API Security
   - Rate limits: per endpoint, per user, per IP
   - Brute force protection on auth endpoints
   - Response sanitisation: which fields must never appear in API responses by role

7. Dependency Security
   - Scan cadence
   - CVE response SLA by severity

8. Logging and Incident Response
   - MUST log (specific event types)
   - MUST NOT log (PII, credentials, tokens)
   - Alerting thresholds
   - Breach notification obligations if applicable

Each requirement: MUST / SHOULD / MAY — no unmarked requirements.

OUTPUT: Write to /documents/Security_Requirements.md
```

---

## 04 · Permissions_Matrix.md

```
Read /documents/Blueprint.md, /documents/PRD.md, and /documents/Security_Requirements.md.

VALIDATION BEFORE WRITING:
- Every role in Blueprint.md must appear as a column
- Every resource and action implied by PRD.md features must appear as a row
- Cross-check: any permission marked MUST in Security_Requirements.md must be reflected in this matrix with explicit enforcement layer

If a role exists in Blueprint.md but has no features in PRD.md, flag it:
⚠️ ORPHAN ROLE: [role] has no associated features — confirm this role belongs in the system

DOCUMENT STRUCTURE:
Write /documents/Permissions_Matrix.md covering:

1. Role Definitions
   For each role: description, who holds it, how assigned, can it coexist with other roles (yes/no + which)

2. Permission Matrix
   Full grid — no abbreviations, no "same as above":
   | Resource · Action | Role A | Role B | Role C | ... |
   Values: ✅ Allowed | ❌ Denied | ⚠️ Conditional

3. Conditional Permission Definitions
   For every ⚠️: exact condition in plain language, enforcement layer (DB / service / gateway / client)

4. Ownership Rules
   - Creator ownership: who owns a record at creation
   - Transfer conditions
   - Shared ownership: allowed or not, and under what rules

5. Role Assignment and Escalation
   - Who can grant each role
   - Who can revoke each role
   - Break-glass mechanism: does one exist, what triggers it, how is it audited

6. Enforcement Layer Map
   For sensitive operations: list every layer that enforces it (must have at minimum: service layer + database)

OUTPUT: Write to /documents/Permissions_Matrix.md
```

---

## 05 · SRS.md

```
Read /documents/Blueprint.md, /documents/PRD.md, /documents/Technical_Requirements.md, and /documents/Permissions_Matrix.md.

VALIDATION BEFORE WRITING:
Run a traceability pre-check:
- Every Feature ID in PRD.md must produce at least one FR-[ID] here
- Every NFR in PRD.md must produce at least one NFR-[ID] here
- Every permission in Permissions_Matrix.md must have a corresponding FR that enforces it
Report any gap as: ⚠️ TRACEABILITY GAP: [description] — then continue

DOCUMENT STRUCTURE:
Write /documents/SRS.md covering:

1. Introduction
   - Document purpose and scope
   - Definitions and abbreviations (only terms actually used in this document)
   - Intended audience
   - Version: 0.1 | Date: [today]

2. System Description
   - System context: users, this system, external systems it touches
   - Context diagram in Mermaid
   - Environmental assumptions

3. Functional Requirements
   For each requirement:
   - ID: FR-[MODULE]-[NUMBER]
   - Statement: "The system SHALL [action] [condition] [outcome]" — no ambiguous language
   - Rationale: one sentence
   - Source: [Feature ID from PRD.md]
   - Verification: Inspection | Demonstration | Test | Analysis
   - Priority: Must | Should | Could | Won't (MoSCoW)

4. Non-Functional Requirements
   Same format, ID: NFR-[CATEGORY]-[NUMBER]
   Categories: Performance | Security | Reliability | Usability | Maintainability | Portability

5. External Interface Requirements
   - User interfaces: functional purpose per screen (not design)
   - Hardware: sensors, camera, Bluetooth, biometrics if applicable
   - Software: every third-party dependency and its interface contract
   - Communication: protocols, ports, data formats

6. Constraints
   - Regulatory
   - Hardware
   - Technology (from Technical_Requirements.md)

7. Traceability Matrix
   | Business Need (Blueprint) | PRD Feature | SRS Requirement(s) | Verification Method |

OUTPUT: Write to /documents/SRS.md
```

---

## 06 · Architecture.md

```
Read /documents/Blueprint.md, /documents/PRD.md, /documents/Technical_Requirements.md, /documents/Security_Requirements.md, and /documents/SRS.md.

VALIDATION BEFORE WRITING:
- Every P0 feature in PRD.md must be traceable to at least one component in this architecture
- Every MUST requirement in Security_Requirements.md must be enforced by an architectural element
- The stack defined here must exactly match Technical_Requirements.md — flag any deviation:
  ⚠️ STACK DEVIATION: [component] uses [X] here but Technical_Requirements.md specifies [Y]

DOCUMENT STRUCTURE:
Write /documents/Architecture.md covering:

1. Architecture Style
   - Pattern chosen (monolith / modular monolith / microservices / etc.)
   - Justification tied to specific project constraints from Blueprint.md (team size, timeline, scale)
   - Tradeoffs explicitly accepted: what does this pattern make harder

2. System Context Diagram (Mermaid C4 context level)
   Every external actor and system the platform touches

3. Component Architecture (Mermaid C4 container level)
   For each component:
   - Name, technology, responsibility (one sentence)
   - Inputs and outputs
   - Failure behaviour: what happens to the system if this component goes down

4. Data Flow
   For each major use case (one per P0 feature):
   - Happy path: request → component chain → response
   - Failure path: where it can fail and what happens

5. Infrastructure
   - Hosting topology
   - Inter-component communication (protocol, sync vs async)
   - Scalability: which components scale and how
   - Caching: what, where, TTL, invalidation strategy
   - Background jobs: list, trigger, failure handling

6. Mobile Architecture
   - App architecture pattern with justification
   - State management approach
   - Offline strategy: what works offline, sync mechanism, conflict resolution
   - Navigation structure (top-level only — detail in Routes.md)

7. Failure Modes
   For each component: failure scenario → system behaviour → recovery mechanism
   Data consistency guarantee: eventual vs strong, per operation type

8. Architecture Decision Records
   For every significant decision made in this document:
   - Context: what forced this decision
   - Options considered: at least two alternatives with tradeoffs
   - Decision: what was chosen
   - Consequences: what this makes easier, what it makes harder

OUTPUT: Write to /documents/Architecture.md
```

---

## 07 · Database_Schema.md

```
Read /documents/Blueprint.md, /documents/PRD.md, /documents/Permissions_Matrix.md, /documents/Security_Requirements.md, and /documents/Architecture.md.

VALIDATION BEFORE WRITING:
- Every resource in Permissions_Matrix.md must map to at least one table
- Every RESTRICTED or CONFIDENTIAL field in Security_Requirements.md must appear with an encryption note
- Every relationship implied by PRD.md features must be represented as a foreign key — flag any that cannot be cleanly modelled:
  ⚠️ MODELLING GAP: [relationship] is implied by [Feature ID] but cannot be expressed without [decision needed]

DOCUMENT STRUCTURE:
Write /documents/Database_Schema.md covering:

1. ERD — full diagram in Mermaid erDiagram syntax
   Every table, every relationship, every cardinality. No omissions.

2. Table Definitions
   For every table:
   - Name and one-sentence purpose
   - Column table:
     | Column | Type | Nullable | Default | Constraints | Encrypted | Description |
   - Primary key
   - Foreign keys with ON DELETE / ON UPDATE behaviour — justified (CASCADE vs RESTRICT vs SET NULL)
   - Unique constraints with business reason
   - Check constraints with business rule being enforced
   - Indexes: column(s), type (B-tree/GIN/etc.), justification (which query pattern this serves)

3. Soft Delete Policy
   Which tables: soft delete (deleted_at) vs hard delete — justified per data retention requirements

4. Audit Trail
   Which tables require created_at, updated_at, created_by, updated_by
   Enforcement layer: trigger / ORM hook / application — and why

5. Data Integrity Rules
   Business rules enforced at DB level (not just application) — what each constraint prevents

6. Sensitive Data Map
   Table → column → classification → encryption requirement (cross-reference Security_Requirements.md)

7. Migration Strategy
   Naming convention, execution order, rollback procedure for destructive changes

8. Seed Data
   What must exist on first deployment — roles, config, admin bootstrap procedure

OUTPUT: Write to /documents/Database_Schema.md
```

---

## 08 · API_Spec.md

```
Read /documents/PRD.md, /documents/Permissions_Matrix.md, /documents/Architecture.md, and /documents/Database_Schema.md.

VALIDATION BEFORE WRITING:
- Every P0 and P1 feature in PRD.md must have at least one endpoint
- Every action in Permissions_Matrix.md that is not purely client-side must have a corresponding endpoint with role guard
- Every endpoint that reads or writes data must reference a table from Database_Schema.md
Flag: ⚠️ COVERAGE GAP: [Feature ID or Permission] has no endpoint

DOCUMENT STRUCTURE:
Write /documents/API_Spec.md covering:

Summary table at top:
| Method | Path | Description | Auth | Roles | Rate Limit |

Then for every endpoint:

---
**[METHOD] /path**
Description: [what this does]
Auth: Required | None
Roles: [from Permissions_Matrix.md]
Rate Limit: [N req / T sec / per user|IP]
Idempotent: Yes | No

Request:
- Path params: name | type | validation | required
- Query params: name | type | validation | default | required
- Body (JSON schema): full schema with types, required fields, validation rules
- Example request body

Response:
- 200/201: schema + example
- Every error code this endpoint can return: code | condition | response body

Side Effects: [what else changes in DB or external systems]
DB Tables: [which tables from Database_Schema.md this touches]
---

After all endpoints, include:
- Global error envelope format
- Auth flow: obtain token → use token → refresh token → revoke token (with exact requests/responses)
- Pagination contract: method, envelope, max page size
- API versioning strategy

OUTPUT: Write to /documents/API_Spec.md
```

---

## 09 · Screens.md

```
Read /documents/PRD.md and /documents/API_Spec.md.

VALIDATION BEFORE WRITING:
- Every P0 and P1 feature in PRD.md must be reachable from at least one screen
- Every endpoint in API_Spec.md must be called by at least one screen — flag orphaned endpoints:
  ⚠️ ORPHAN ENDPOINT: [endpoint] is not used by any screen
- Every role in PRD.md must have a distinct entry point screen

DOCUMENT STRUCTURE:
Write /documents/Screens.md covering:

For every screen:

---
**SCR-[ROLE]-[NUMBER] — [Screen Name]**
Role: [which user sees this]
Purpose: [one sentence: what decision or action does this enable]
Route reference: [to be filled by Routes.md]

Entry points: [which screens lead here, via which action]

Content inventory:
| Element | Data source | API endpoint | Notes |

Available actions:
| Action | Trigger | Result | API call |

State variations:
- Loading: [behaviour]
- Empty: [copy and action]
- Error: [copy and recovery path]
- Role variant: [if screen differs by role]

Exit points: [screens reachable from here]
---

After all screens:
- Screen flow diagram per role in Mermaid flowchart
- Screens that require confirmation dialogs (list with trigger condition)
- Screens that are modals vs full-screen pushes

OUTPUT: Write to /documents/Screens.md
```

---

## 10 · Routes.md

```
Read /documents/Screens.md and /documents/Permissions_Matrix.md.

VALIDATION BEFORE WRITING:
- Every screen in Screens.md must have exactly one route
- Every role in Permissions_Matrix.md must have a valid post-login destination
- No route that leads to a restricted screen should be reachable without the route guard in place

DOCUMENT STRUCTURE:
Write /documents/Routes.md covering:

1. Route Registry
   | Route ID | Name/Path | Screen ID | Auth Required | Role Guard | Navigator Type |

2. Navigator Tree (Mermaid)
   Full tree: root → auth navigator / app navigator → tab navigators → stack navigators → screens

3. Auth Routing Logic
   - Unauthenticated user hits any route → where do they go
   - Post-login: role → destination screen (one rule per role)
   - Token expiry mid-session: current route preserved or cleared? Exact behaviour.

4. Deep Links
   - Which routes are deep-linkable (yes/no per route)
   - URL scheme / Universal Link pattern
   - Auth gate behaviour: deep link to protected route when unauthenticated → what happens

5. Route Guards
   For each protected route: condition required | failure behaviour (redirect to X | show modal | error screen)

6. Back Navigation Rules
   - Screens that clear the back stack (list)
   - Screens where back is intercepted (with reason and alternative action)

OUTPUT: Write to /documents/Routes.md
```

---

## 11 · Design.md

```
Read /documents/Blueprint.md, /documents/PRD.md, and /documents/Screens.md.

VALIDATION BEFORE WRITING:
- Confirm the target platform from Blueprint.md — design tokens must be appropriate for that platform (Android Material You vs iOS HIG vs Web)
- Every screen in Screens.md must be coverable by the component library defined here — flag any screen that implies a component not in the standard set:
  ⚠️ MISSING COMPONENT: SCR-[ID] requires [component] not in library

DOCUMENT STRUCTURE:
Write /documents/Design.md covering:

1. Design Principles
   3–5 principles specific to this product. Each must be concrete enough to resolve a real design conflict — reject any principle that applies equally to all software.

2. Visual Identity
   - Color tokens (hex values):
     primary, primary-variant, secondary, surface, surface-variant,
     background, error, warning, success, info,
     on-primary, on-surface, on-background, on-error
   - Typography:
     | Role | Typeface | Size | Weight | Line Height | Usage |
   - Spacing system: base unit, scale steps
   - Border radius tokens: none, sm, md, lg, full
   - Elevation/shadow tokens: 0–5
   - Icon library: which one, usage rules, size grid

3. Component Library
   For each component (Button, Input, Card, List Item, Bottom Sheet, Dialog, Toast, Badge, Tab, Navigation Bar, Empty State, Skeleton, Chip):
   - Visual spec: size, padding, shape
   - States: default | pressed | focused | disabled | error | loading
   - Usage rule: when to use this vs the alternative
   - Accessibility: min touch target, contrast ratio, ARIA/content description

4. UX Patterns
   - Form validation: when to validate (on blur / on submit / on change), error placement
   - Loading: skeleton vs spinner — decision rule
   - Empty states: required elements (illustration | heading | body | CTA)
   - Error handling: toast vs inline vs full-page — decision rule with examples
   - Destructive actions: confirmation pattern (what qualifies as destructive)
   - Pull to refresh: when allowed

5. Accessibility Standards
   - WCAG level: AA minimum
   - Touch target: 48dp minimum
   - Contrast ratios: normal text, large text, UI components
   - Dynamic type: supported size range
   - Screen reader: content description policy

6. Motion Tokens
   - Duration: instant (0ms), fast (100ms), standard (200ms), slow (300ms)
   - Easing curves per transition type
   - Reduced motion: what collapses to cut

OUTPUT: Write to /documents/Design.md
```

---

## 12 · UI_Prompts.md

```
Read /documents/Design.md, /documents/Screens.md, and /documents/Routes.md.

VALIDATION BEFORE WRITING:
- Every screen in Screens.md must have a corresponding prompt block
- Every color token referenced in prompts must exist in Design.md
- Every component referenced must exist in Design.md component library

DOCUMENT STRUCTURE:
Write /documents/UI_Prompts.md

For every screen in Screens.md:

---
## SCR-[ROLE]-[NUMBER] — [Screen Name]

### Figma AI
[Self-contained. Include: screen name and purpose, user role, exact layout description, every visible UI element, interaction states, color tokens from Design.md by name and hex, typography tokens by role name and size, spacing values, platform (Android/iOS/Web). Assume Figma AI has read nothing else. Every detail must be in this prompt.]

### Bolt / Lovable / v0 (Component Code)
[Self-contained. Include: framework and version (from Technical_Requirements.md), component name and file path, TypeScript interface for props, all data it receives and its shape, all callbacks it emits, exact color tokens as variables, responsive breakpoints, accessibility props required, which API endpoint(s) it calls and the expected response shape. No external references — everything inline.]

### Gemini / Image Generation (Mockup)
[Self-contained. Include: device frame (Android phone, iOS phone, desktop browser), exact screen content and layout in plain language, color scheme by hex, typography style, realistic sample data (not "Lorem ipsum" — use contextually appropriate fake data), UI style matching Design.md, key interaction states to show if multiple states needed.]

---

After all per-screen prompts:

## Global Component Prompts
One prompt per shared component from Design.md component library.
Each prompt must produce a standalone component with all states.

## Design Token Prompt
A single prompt that produces the complete design token file for this project.
Format: Kotlin (if Android), Swift (if iOS), CSS variables (if Web), or all three.

OUTPUT: Write to /documents/UI_Prompts.md
```

---

## 13 · Testing_Strategy.md

```
Read ALL files in /documents/.

VALIDATION BEFORE WRITING:
Run coverage pre-check and report before generating:
- Every P0 feature in PRD.md: does it have unit tests + integration tests + UAT scenario? Flag missing:
  ⚠️ TEST GAP: [Feature ID] has no [test type]
- Every endpoint in API_Spec.md: does it have an integration test? Flag missing.
- Every CRITICAL and HIGH threat in Security_Requirements.md: does it have a security test? Flag missing.
Report all gaps, then proceed.

DOCUMENT STRUCTURE:
Write /documents/Testing_Strategy.md

---
### Part 1 — Unit Tests

For every service, utility, and business logic function in Architecture.md and API_Spec.md:
- Function name, module, business rule it enforces
- Test cases:
  - Happy path: input → expected output
  - Boundary: empty, null, max, min
  - Invalid input: → expected error
  - Business-rule edge cases specific to this function
- Mock strategy: what to mock, what not to (justify each)
- Coverage target: line % and branch % per module

---
### Part 2 — Integration Tests

For every endpoint in API_Spec.md:
- ID: IT-[MODULE]-[NUMBER]
- Endpoint
- Scenario
- Preconditions: DB state, auth role, existing records
- Request: exact headers, body, params
- Expected response: status, body schema, DB side effects
- Cleanup: what resets after this test

Also include:
- Auth flow tests: login, refresh, logout, expired token, revoked token
- Permission enforcement: one test per role per restricted endpoint (use Permissions_Matrix.md)
- Third-party mocks: network boundary mocking strategy

---
### Part 3 — Security Tests

For each threat in Security_Requirements.md STRIDE table (CRITICAL and HIGH):
- ID: ST-[THREAT]-[NUMBER]
- Threat and attack vector
- Exact test steps (HTTP requests or UI actions)
- Expected secure system response
- Severity if failing: CRITICAL / HIGH / MEDIUM / LOW

Required coverage:
- Auth bypass: missing token, invalid token, expired token, token for wrong role
- Horizontal privilege escalation: user accessing another user's resources
- Vertical privilege escalation: low-privilege role calling restricted endpoint
- Injection: SQL injection on every string input, NoSQL injection where applicable
- Mass assignment: extra fields in POST/PUT bodies
- Rate limiting: confirm limits from API_Spec.md are enforced
- PII exposure: confirm restricted fields absent from responses for non-authorised roles
- Sensitive data in logs: confirm tokens and PII do not appear in application logs

---
### Part 4 — UAT Scenarios

For each persona in PRD.md, for each P0 and P1 feature they own:
- ID: UAT-[PERSONA]-[NUMBER]
- User goal (from PRD.md user story)
- Preconditions: exact system state before user begins
- Steps: written from user perspective, zero technical language
- Expected outcome per step
- Pass criteria: specific, measurable, no interpretation required
- Edge cases within this scenario
- PRD acceptance criteria validated: [list AC IDs]

---
### Appendix — Test Infrastructure
- Framework per layer (unit / integration / security / E2E)
- CI/CD: which tests run on PR | on merge to main | on release candidate
- Test data: factories vs fixtures vs seeds — strategy and examples
- Environment requirements per suite

OUTPUT: Write to /documents/Testing_Strategy.md
```

---

## Usage Reference

### Execution Order
```
[YOU WRITE]  /documents/Blueprint.md
                    │
             00 · Blueprint Review Gate  ← resolve all blockers before continuing
                    │
             01 · PRD.md
                    │
          ┌─────────┼──────────┐
          │         │          │
    02 · Tech   03 · Security  04 · Permissions
    Req.md      Req.md         Matrix.md
          │         │          │
          └─────────┴──────────┘
                    │
             05 · SRS.md
                    │
             06 · Architecture.md
                    │
             07 · Database_Schema.md
                    │
             08 · API_Spec.md
                    │
          ┌─────────┴──────────┐
          │                    │
    09 · Screens.md      (testing input)
          │
    10 · Routes.md
          │
    11 · Design.md
          │
    12 · UI_Prompts.md

    13 · Testing_Strategy.md  ← reads ALL of /documents/
```

### Hard Rules for IDE Use
- Always start a session by telling the AI: **"All source documents are in /documents/. Do not ask me to paste content."**
- If the AI rewrites a prior document without being asked: reject it. Each prompt writes exactly one new file.
- If the AI resolves a `[DECISION NEEDED]` silently without flagging it: reject that output. Force it to surface the decision.
- After running Blueprint Review Gate (00), resolve every MUST RESOLVE item before running prompt 01. Unresolved blockers in Blueprint.md compound into every downstream document.
- When you update Blueprint.md after locking it: re-run the Review Gate, then re-run every document that depends on the changed section. Do not patch documents individually.

### Red Flags in AI Output
These indicate weak or hallucinated output — regenerate if found:
- Acceptance criteria containing adjectives without measures ("responsive", "fast", "intuitive")
- Security requirements not referencing a specific threat from the threat model
- DB foreign keys without ON DELETE behaviour specified
- API endpoints with no error response definitions
- Architecture decisions with no stated tradeoffs
- Test cases covering only happy paths
- Permissions in the matrix with no enforcement layer specified
- Any document that contradicts a prior document without a ⚠️ CONFLICT flag
