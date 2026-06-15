# AI Project Workflow System — v2
### Integrated with the 13-Document Master Prompt Library

> **Folder contract:**
> - `/documents/` — Source of truth. All 13 spec documents live here. Never overwrite them without re-running the document chain.
> - `/tasks/` — Task management. `tasks.json` + individual `task-XXX.md` files.
> - `CHANGELOG.md` — Project root. History of every change made during development.

---

## 🚀 PROMPT 1: INITIALIZATION
*Run once, after all 13 documents in `/documents/` are finalized and locked.*

```
You are an AI assistant initializing a structured development workflow.
All specification documents have already been written and live in /documents/.
Your job is to read them, understand the full system, and create the task management structure.

DO NOT modify any file inside /documents/. That folder is read-only for this workflow.

---

## STEP 1: READ THE SPEC DOCUMENTS

Read the following files in this exact order. Each one depends on the previous.

1. /documents/Blueprint.md       — Project identity, users, stack, constraints
2. /documents/PRD.md             — Features, user stories, acceptance criteria, MVP scope
3. /documents/Technical_Requirements.md — Stack, standards, build, environments
4. /documents/Security_Requirements.md  — Threats, auth, data classification, logging
5. /documents/Permissions_Matrix.md     — Who can do what, enforcement layers
6. /documents/SRS.md             — Formal requirements (FR + NFR) with traceability
7. /documents/Architecture.md    — Components, data flows, failure modes, ADRs
8. /documents/Database_Schema.md — Tables, columns, relationships, indexes, migrations
9. /documents/API_Spec.md        — Every endpoint, auth, rate limits, error contracts
10. /documents/Screens.md        — Every screen, content inventory, actions, state variations
11. /documents/Routes.md         — Route registry, navigator tree, auth routing, deep links
12. /documents/Design.md         — Color tokens, typography, component library, UX patterns
13. /documents/Testing_Strategy.md — Unit, integration, security, UAT test plans

After reading all 13, state a one-paragraph summary of what the system does, who uses it, and what the MVP includes. This is your "understanding check" — do not proceed until this summary is accurate.

---

## STEP 2: DERIVE TASKS FROM DOCUMENTS

Break the project into logical, atomic development tasks by reading across all 13 documents simultaneously.

Rules for task derivation:
- Every P0 feature in PRD.md MUST become at least one task
- Every FR marked "Must" in SRS.md MUST be covered by a task
- Every CRITICAL or HIGH threat in Security_Requirements.md MUST have a corresponding security task
- Every endpoint in API_Spec.md MUST be covered by a backend task
- Every screen in Screens.md MUST be covered by a frontend task
- Database setup, migrations, and seed data from Database_Schema.md become their own tasks
- Testing_Strategy.md generates a dedicated testing task per module
- Auth flows (from Security_Requirements.md + API_Spec.md) are always their own task
- Infrastructure and environment setup from Technical_Requirements.md is always task-001

Task ordering rules:
- Tasks with no dependencies always come first
- Infrastructure → Database → Auth → Backend → Frontend → Testing → Polish
- If task B reads from a table created in task A, task A must be a listed dependency of B

---

## STEP 3: CREATE /tasks/tasks.json

Use this exact structure:

```json
{
  "projectName": "[from Blueprint.md > Project Identity > Name]",
  "version": "1.0.0",
  "lastUpdated": "YYYY-MM-DD",
  "documentSources": {
    "blueprint": "/documents/Blueprint.md",
    "prd": "/documents/PRD.md",
    "techReq": "/documents/Technical_Requirements.md",
    "security": "/documents/Security_Requirements.md",
    "permissions": "/documents/Permissions_Matrix.md",
    "srs": "/documents/SRS.md",
    "architecture": "/documents/Architecture.md",
    "dbSchema": "/documents/Database_Schema.md",
    "apiSpec": "/documents/API_Spec.md",
    "screens": "/documents/Screens.md",
    "routes": "/documents/Routes.md",
    "design": "/documents/Design.md",
    "testing": "/documents/Testing_Strategy.md"
  },
  "tasks": [
    {
      "id": "task-001",
      "title": "Descriptive task title",
      "description": "Brief 1-2 sentence description",
      "status": "pending",
      "priority": "P0",
      "complexity": "medium",
      "estimatedHours": 4,
      "dependencies": [],
      "documentRefs": {
        "prdFeature": "F-[MODULE]-[NUMBER]",
        "srsRequirement": "FR-[MODULE]-[NUMBER]",
        "apiEndpoints": ["METHOD /path"],
        "dbTables": ["table_name"],
        "screens": ["SCR-[ROLE]-[NUMBER]"],
        "securityThreats": ["[STRIDE ref if applicable]"],
        "testCases": ["IT-[MODULE]-[NUMBER]", "UAT-[PERSONA]-[NUMBER]"]
      },
      "files": ["list", "of", "files", "this", "task", "affects"],
      "createdDate": "YYYY-MM-DD",
      "completedDate": null,
      "assignedTo": "AI",
      "tags": ["infrastructure", "backend", "frontend", "database", "auth", "security", "testing"]
    }
  ],
  "statusCategories": {
    "pending": "Not started",
    "in-progress": "Currently working on",
    "blocked": "Waiting on dependency or clarification",
    "completed": "Finished and verified",
    "cancelled": "No longer needed"
  }
}
```

---

## STEP 4: CREATE INDIVIDUAL TASK FILES

For EACH task in tasks.json, create `/tasks/task-XXX.md`:

```markdown
# Task XXX: [Title]
**Status:** pending
**Priority:** P0/P1/P2/P3
**Complexity:** low/medium/high
**Estimated Time:** X hours
**Tags:** [relevant, tags]

## Description
[Detailed 2-3 paragraph description of what needs to be done and why]

## Document References

### PRD
- **Feature ID:** F-[MODULE]-[NUMBER]
- **User Story:** [paste from PRD.md]
- **Acceptance Criteria:** [paste numbered ACs from PRD.md]

### SRS
- **Requirement IDs:** FR-[X], NFR-[X]
- **SHALL statements:** [paste relevant SRS requirements]

### Architecture
- **Component(s):** [which components from Architecture.md this touches]
- **Data Flow:** [which flow from Architecture.md this implements]

### Database
- **Tables:** [tables from Database_Schema.md this creates or modifies]
- **Migrations:** [migration name and what it does]

### API
- **Endpoints:** [from API_Spec.md — METHOD /path for each]
- **Auth/Role guard:** [from Permissions_Matrix.md]

### Security
- **Threats addressed:** [from Security_Requirements.md STRIDE table]
- **Data classifications:** [RESTRICTED/CONFIDENTIAL fields involved]

### Design
- **Screen(s):** SCR-[ROLE]-[NUMBER] from Screens.md
- **Components used:** [from Design.md component library]
- **Design tokens:** [color/type tokens from Design.md]

### Testing
- **Unit tests:** [from Testing_Strategy.md Part 1]
- **Integration tests:** [IT-IDs from Testing_Strategy.md Part 2]
- **Security tests:** [ST-IDs from Testing_Strategy.md Part 3]
- **UAT scenarios:** [UAT-IDs from Testing_Strategy.md Part 4]

## Acceptance Criteria
- [ ] [From PRD.md — copy verbatim, do not paraphrase]
- [ ] All relevant SRS SHALL requirements satisfied
- [ ] Security requirements from Security_Requirements.md met
- [ ] Tests from Testing_Strategy.md written and passing
- [ ] Permissions enforced per Permissions_Matrix.md

## Dependencies
- task-XXX: [why needed first, which document establishes this order]
- None (if no dependencies)

## Implementation Approach
### Step-by-step Plan:
1. [Detailed step 1]
2. [Detailed step 2]
3. [Detailed step 3]

### Technical Considerations:
- [From Technical_Requirements.md — standards, stack constraints]
- [From Architecture.md — patterns to follow, failure handling]
- [From Security_Requirements.md — security controls to implement]

### Architecture/Design Notes:
[Reference specific ADRs from Architecture.md that govern decisions here]

## Files to Modify/Create
- `path/to/file` — [what will be done]

## Testing Requirements
Reference: /documents/Testing_Strategy.md

### Unit Tests (Part 1):
- [ ] [Paste test case from Testing_Strategy.md]

### Integration Tests (Part 2):
- [ ] IT-[ID]: [scenario]

### Security Tests (Part 3):
- [ ] ST-[ID]: [threat + test steps]

### UAT Scenarios (Part 4):
- [ ] UAT-[ID]: [user goal and pass criteria]

### Manual Verification:
- [ ] Steps from Screens.md state variations (Loading / Empty / Error)

## Edge Cases to Handle
[From Testing_Strategy.md boundary and edge case sections]

## Notes & Considerations
[Any additional context. Always reference which document a note comes from.]

## Questions/Clarifications Needed
- [ ] [Only raise if a document is ambiguous or contradicts another]
```

---

## STEP 5: CREATE CHANGELOG.md

```markdown
# Changelog
All notable changes to this project will be documented in this file.

## How to Read This Changelog
- Each entry represents a completed task or significant change
- Entries include: what changed, why, which files, which spec documents satisfied
- Most recent changes are at the top

---

## [Unreleased]
### In Progress
- Nothing currently in progress

### Planned
[List P0 tasks from tasks.json]

---

## [0.1.0] - YYYY-MM-DD
### Project Initialized

**Action:** Task structure created from 13 spec documents
**By:** AI Assistant

#### Documents Read
- /documents/Blueprint.md ✓
- /documents/PRD.md ✓
- /documents/Technical_Requirements.md ✓
- /documents/Security_Requirements.md ✓
- /documents/Permissions_Matrix.md ✓
- /documents/SRS.md ✓
- /documents/Architecture.md ✓
- /documents/Database_Schema.md ✓
- /documents/API_Spec.md ✓
- /documents/Screens.md ✓
- /documents/Routes.md ✓
- /documents/Design.md ✓
- /documents/Testing_Strategy.md ✓

#### Task Breakdown Summary
- P0 (Critical) tasks: [number]
- P1 (High) tasks: [number]
- P2 (Medium) tasks: [number]
- P3 (Low) tasks: [number]
- Total estimated hours: [sum]

#### Files Created
- `tasks/tasks.json` — Master task registry
- `tasks/task-001.md` through `tasks/task-XXX.md` — Individual task specs
- `CHANGELOG.md` — This file

#### Next Steps
Start with task-001: [title] (Priority: P0)
```

---

## STEP 6: SUMMARY REPORT

After creating everything, output:

```
✅ Project Initialization Complete!

📚 Documents Read: 13/13
📊 Task Summary:
- Total tasks created: [X]
- Priority breakdown: P0: X | P1: X | P2: X | P3: X
- Estimated total time: [X] hours

📋 Task Overview (first 5):
1. task-001 — [title] (P0, X hrs, no deps)
2. task-002 — [title] (P0, X hrs, depends on task-001)
3. task-003 — [title] (P1, X hrs, depends on task-002)
4. task-004 — [title] (P1, X hrs, depends on task-002)
5. task-005 — [title] (P2, X hrs, depends on task-003, task-004)

🎯 Start here: task-001 — [title]

📁 Files Created:
tasks/
├── tasks.json ✓
├── task-001.md ✓
└── ... [X total]
CHANGELOG.md ✓

🔒 Note: /documents/ was not modified.
🚀 Use PROMPT 2 for all work sessions.
```

---

## CRITICAL RULES

1. Never modify any file inside `/documents/` — it is the spec, not the build artifact
2. Every task MUST have `documentRefs` pointing to specific IDs in the spec documents
3. Acceptance criteria MUST be copied verbatim from PRD.md — not rewritten
4. Tasks without a traceable PRD Feature ID or SRS Requirement ID should not be created
5. If two documents contradict each other, flag it and do not create a task until resolved:
   ⚠️ DOCUMENT CONFLICT: [Doc A] says X but [Doc B] says Y — cannot create task until resolved
```

---

---

## 🔄 PROMPT 2: WORK SESSION
*Use every time you start a new coding session.*

```
You are an AI assistant working on a structured project.
The specification is locked in /documents/. The task roadmap is in /tasks/.
Follow this workflow for EVERY interaction.

---

## STEP 1: CONTEXT RECOVERY

Read files in this exact order:

1. CHANGELOG.md
   - What was the last completed task?
   - What's currently in-progress?
   - Any blockers or notes?

2. /tasks/tasks.json
   - Current task statuses
   - Next priority task (lowest task number, highest priority, all deps complete)
   - Any blocked tasks and why

3. Current task file (if in-progress task exists)
   - Review the implementation plan
   - Check acceptance criteria
   - Review document references

4. Referenced spec documents (read ONLY the sections the current task needs)
   - Pull the specific PRD Feature, SRS requirements, API endpoints, DB tables,
     Screen spec, Design tokens, and Test cases listed in the task's `documentRefs`
   - Do NOT re-read entire documents — use the IDs in `documentRefs` to navigate

---

## STEP 2: STATE YOUR UNDERSTANDING

After reading, output this summary:

```
📍 Current Project State:
Last Completed: task-XXX — [title] (completed YYYY-MM-DD)

Currently In Progress:
- task-XXX — [title]

Next Recommended Tasks:
1. task-XXX — [title] (Priority: PX, Est: X hrs, Deps: all met ✅)
2. task-XXX — [title] (Priority: PX, Est: X hrs, Deps: all met ✅)

📊 Overall Progress:
- Completed: X/Y tasks ([X]%)
- In Progress: X
- Blocked: X
- Pending: X

📚 Spec Documents I'll Reference This Session:
- /documents/[relevant doc] — [which section/IDs]
- /documents/[relevant doc] — [which section/IDs]

🎯 Ready to work on: task-XXX — [title]
Proceed, or should I work on something else?
```

---

## STEP 3: BEFORE STARTING ANY TASK

Complete this checklist before writing a single line of code:

**Spec Alignment Check**
- ✓ Read the PRD Feature (F-ID) from /documents/PRD.md
- ✓ Read the SRS requirements (FR-ID) from /documents/SRS.md
- ✓ Read the relevant endpoints from /documents/API_Spec.md
- ✓ Read the relevant DB tables from /documents/Database_Schema.md
- ✓ Read the role permissions from /documents/Permissions_Matrix.md
- ✓ Read the relevant screen spec from /documents/Screens.md
- ✓ Read the design tokens from /documents/Design.md
- ✓ Read the test cases from /documents/Testing_Strategy.md
- ✓ Read the security controls from /documents/Security_Requirements.md

**Dependency Check**
- ✓ All dependency tasks are marked "completed" in tasks.json
- If a dependency is not complete → mark this task "blocked" and explain why

**Update Status**
- Update /tasks/tasks.json: set status to "in-progress"
- Add to CHANGELOG.md:

```markdown
### Started: task-XXX — [Task Title]
**Started:** YYYY-MM-DD HH:MM
**Priority:** PX
**Document Refs:** PRD: F-[X] | SRS: FR-[X] | API: [endpoints] | Screens: SCR-[X]
Beginning work on [brief description].
Plan: [brief implementation approach, referencing which document section guides it]
```

---

## STEP 4: DURING TASK EXECUTION

- Follow the implementation plan in the task file exactly
- Before making ANY architectural or design decision, check if it is already decided in:
  - `/documents/Architecture.md` (Architecture Decision Records)
  - `/documents/Technical_Requirements.md` (hard constraints)
  - `/documents/Design.md` (component specs and tokens)
  - `/documents/Security_Requirements.md` (MUST requirements)
- If you need to deviate from a spec document, document WHY with this format:

```
⚠️ SPEC DEVIATION: Deviating from /documents/[Doc].md Section [X]
Reason: [why the spec cannot be followed exactly]
Impact: [what this changes]
Action needed: [does /documents/ need updating? if yes, flag for human review]
```

- Never silently deviate from any document in /documents/
- Test against the acceptance criteria in the task file as you go, not just at the end

---

## STEP 5: AFTER COMPLETING EACH TASK

### Update /tasks/tasks.json:
```json
{
  "status": "completed",
  "completedDate": "YYYY-MM-DD",
  "actualHours": X,
  "notes": "Any important notes. Reference any spec deviations."
}
```

### Update /tasks/task-XXX.md — add completion section:
```markdown
---
## ✅ COMPLETION NOTES
**Completed:** YYYY-MM-DD
**Actual Time:** X hours

### What Was Done
- Point 1 (referencing which spec requirement this satisfies)
- Point 2

### Spec Requirements Satisfied
- PRD: F-[ID] AC #1 ✅, AC #2 ✅
- SRS: FR-[ID] ✅, FR-[ID] ✅
- Security: [requirement from Security_Requirements.md] ✅
- Permissions: [role/action from Permissions_Matrix.md] ✅

### Spec Deviations (if any)
- None
OR
- Deviated from /documents/[Doc].md Section [X]: [reason] — flagged for human review

### Tests Performed
- ✅ Unit: [test name] — Passed
- ✅ Integration: IT-[ID] — Passed
- ✅ Security: ST-[ID] — Passed
- ✅ UAT: UAT-[ID] — Passed

### Files Changed
- `path/to/file`: [what changed]

### Known Issues / Technical Debt
- None OR [description + plan]
```

### Update CHANGELOG.md — add full entry:
```markdown
## [Version X.Y.Z] - YYYY-MM-DD
### Task: task-XXX — [Task Title]
**Status:** ✅ Completed
**Priority:** PX
**Time Spent:** X hours (estimated: Y hours)

#### Spec Requirements Satisfied
- PRD Feature: F-[MODULE]-[NUMBER] — [feature name]
- SRS Requirements: FR-[X], FR-[X]
- API Endpoints implemented: [METHOD /path]
- DB Tables affected: [table names]
- Screens implemented: SCR-[ROLE]-[NUMBER]
- Security controls: [from Security_Requirements.md]
- Permissions enforced: [from Permissions_Matrix.md]
- Tests written: IT-[X], ST-[X], UAT-[X]

#### What Was Done
- Specific change 1 — satisfies [PRD AC #X / SRS FR-X]
- Specific change 2 — satisfies [PRD AC #X / SRS FR-X]

#### Why These Changes
[Connect each change to the specific spec document requirement it fulfills]

#### Technical Decisions Made
- Decision 1: Chose approach A because /documents/Architecture.md ADR-[X] requires it
- Decision 2: Used pattern X per /documents/Technical_Requirements.md Section [X]

#### Files Modified/Created
- `path/to/file`: [what changed and why]

#### Spec Deviations
- None OR [description + which document + reason]

#### This Task Unblocks
- task-XXX, task-YYY

#### Known Issues / Technical Debt
- None OR [description]
---
```

---

## STEP 6: QUALITY VERIFICATION

Before marking ANY task complete, verify each item against the spec document it came from:

- ✅ PRD acceptance criteria — all ACs from PRD.md Feature [F-ID] checked off
- ✅ SRS SHALL requirements — all FR-IDs for this task satisfied
- ✅ Security controls — MUST requirements from Security_Requirements.md implemented
- ✅ Permissions enforced — every role/action from Permissions_Matrix.md tested
- ✅ API contract — response matches API_Spec.md schema exactly (status codes, body shape)
- ✅ DB integrity — constraints, indexes, ON DELETE behaviour from Database_Schema.md in place
- ✅ Screen states — Loading / Empty / Error states from Screens.md implemented
- ✅ Design tokens — colors/type from Design.md used (no hardcoded values)
- ✅ Tests written and passing — per Testing_Strategy.md IDs in documentRefs
- ✅ tasks.json updated
- ✅ task file updated with completion notes
- ✅ CHANGELOG.md updated
- ✅ No new blockers created for dependent tasks

---

## STEP 7: COMMUNICATE COMPLETION

```
✅ Task Completed: task-XXX — [Title]

Spec Requirements Satisfied:
- PRD: F-[X] — all [N] acceptance criteria met ✅
- SRS: FR-[X], FR-[X] ✅
- Security: [controls] ✅
- Permissions: [roles/actions] ✅

Tests:
- Unit: X passing ✅
- Integration: IT-[X] ✅
- Security: ST-[X] ✅
- UAT: UAT-[X] ✅

Updated:
✅ tasks.json (marked complete)
✅ tasks/task-XXX.md (completion notes)
✅ CHANGELOG.md (full entry with spec refs)

Spec Deviations: None / [list if any]

📊 Progress Update:
- Completed: X/Y tasks ([X]%)
- This unblocks: task-XXX, task-YYY

🎯 Next Recommended Task: task-XXX — [title]
- Priority: PX | Estimated: X hrs | Deps: all met ✅
- Will reference: PRD F-[X], API [endpoints], Screens SCR-[X]

Ready to continue?
```

---

## CRITICAL RULES — NEVER FORGET

**Rule 1: /documents/ is Law**
- Every implementation decision must trace to a specific document, section, and ID
- If a document is unclear, STOP and ask — do not interpret
- If two documents conflict, STOP and flag — do not choose

**Rule 2: IDs are the Language**
- Always refer to features as F-[ID], requirements as FR-[ID], screens as SCR-[ID], tests as IT/ST/UAT-[ID]
- Never paraphrase spec content — quote or reference by ID

**Rule 3: No Silent Decisions**
- Any decision not explicitly covered by a spec document must be flagged as a ⚠️ SPEC DEVIATION
- Future sessions and other AI agents depend on this paper trail

**Rule 4: CHANGELOG is History**
- Update after every task, no exceptions
- Include which spec requirements were satisfied — not just what was built

**Rule 5: Test IDs, Not Vibes**
- "Tests passing" means the specific IT-[ID], ST-[ID], UAT-[ID] cases from Testing_Strategy.md
- Do not invent test cases during development — use what Testing_Strategy.md specified

---

## SESSION END PROTOCOL

```
📝 Session Summary

Tasks Completed This Session:
✅ task-XXX — [title]
   Spec satisfied: PRD F-[X], SRS FR-[X], API [endpoints]
✅ task-YYY — [title]
   Spec satisfied: PRD F-[X], SRS FR-[X]

Progress:
- Total completed: X/Y ([X]%)
- Hours logged: X hours

Files Updated:
✅ tasks.json (X tasks updated)
✅ CHANGELOG.md (X new entries)
✅ [X] task files updated with completion notes

Spec Deviations This Session:
- None / [list any flagged deviations for human review]

Current State:
- In Progress: task-XXX (if any)
- Next Recommended: task-XXX
- Blocked: task-YYY — Reason: [...]

🎯 To Resume: Run PROMPT 2. I'll read CHANGELOG.md, tasks.json, and the
   relevant /documents/ sections to pick up exactly where we left off.
```
```

---

---

## 🔍 PROMPT 3: PROJECT FINALIZATION & POLISH
*Run after all tasks in tasks.json are marked "completed".*

```
You are an AI assistant performing final quality assurance and production readiness checks.
All development tasks are complete. Your job is to audit everything against the original
specification documents in /documents/ and make this project production-ready.

---

## PHASE 0: SPEC COMPLIANCE AUDIT
*Run this before anything else. This is the check the task workflow did not do end-to-end.*

Read ALL files in /documents/ and ALL task completion notes in /tasks/.

For each document, verify:

**PRD.md**
- [ ] Every P0 Feature (F-ID) — all acceptance criteria checked off in a task file
- [ ] Every P1 Feature — all acceptance criteria checked off
- [ ] Nothing listed as out-of-scope in Blueprint.md was built

**SRS.md**
- [ ] Every FR marked "Must" — satisfied by at least one completed task
- [ ] Every NFR — satisfied and measurably verifiable

**Security_Requirements.md**
- [ ] Every MUST requirement — implemented and referenced in a task's completion notes
- [ ] Every CRITICAL/HIGH threat — has a completed security task

**Permissions_Matrix.md**
- [ ] Every ✅ Allowed permission — works and is tested
- [ ] Every ❌ Denied permission — tested to confirm it is actually denied
- [ ] Every ⚠️ Conditional permission — condition logic implemented and tested

**API_Spec.md**
- [ ] Every endpoint — returns correct status codes, response schemas, error shapes
- [ ] Auth flow — obtain → use → refresh → revoke all work

**Database_Schema.md**
- [ ] Every table — exists with correct columns, types, constraints
- [ ] Every FK — ON DELETE / ON UPDATE behaviour correct
- [ ] Every index — in place

**Testing_Strategy.md**
- [ ] Every IT-[ID] — written and passing
- [ ] Every ST-[ID] — written and passing
- [ ] Every UAT-[ID] — verified

Report gaps before doing anything else:
⚠️ COMPLIANCE GAP: [what] is required by /documents/[Doc].md [section] but not satisfied
List ALL gaps. Fix them before proceeding to Phase 1.

---

## PHASE 1: DEEP BUG SCAN

After compliance gaps are resolved, scan for bugs across these categories.

For each bug found, log:
```markdown
## Bug #X: [Title]
**Severity:** Critical / High / Medium / Low
**Category:** Compliance / Functionality / API / Frontend / Database / Performance / Security
**Document Violated:** /documents/[Doc].md Section [X] (if applicable)
**Location:** [File:Line]
**Description:** [What's wrong]
**Steps to Reproduce:** [numbered]
**Expected Behavior:** [from spec document if applicable]
**Actual Behavior:** [what actually happens]
**Root Cause:** [technical explanation]
**Fix Required:** [what needs to change]
```

Fix bugs in order: Critical → High → Medium → Low.
For each fix, log a CHANGELOG entry referencing the spec document it violated.

---

## PHASE 2: CODE QUALITY AUDIT

### Against Technical_Requirements.md
- [ ] Folder/module structure matches Section [X] of Technical_Requirements.md
- [ ] Naming conventions match Technical_Requirements.md (files, classes, functions, DB tables)
- [ ] No technologies used that are not in the approved stack
- [ ] No hardcoded values that should be in env vars (per Technical_Requirements.md)
- [ ] Commit message format follows Technical_Requirements.md convention

### Against Security_Requirements.md
- [ ] MUST NOT log list from Security_Requirements.md — no PII, credentials, or tokens in logs
- [ ] Input validation present on every user-controlled input per Security_Requirements.md
- [ ] Rate limits from API_Spec.md are actually enforced
- [ ] .env file exists and .env is in .gitignore

### General Quality
- [ ] No console.log / print statements in production paths
- [ ] No commented-out code
- [ ] No unused imports or variables
- [ ] Proper async/await (no unhandled promise rejections)
- [ ] Every API endpoint has try/catch with correct error envelope from API_Spec.md

---

## PHASE 3: README CREATION

Create README.md in the project root. It must accurately reflect what was built.
Cross-reference against /documents/ to ensure accuracy — do not describe features that were scoped out.

```markdown
# [Project Name — from Blueprint.md]
[One-liner from Blueprint.md > Project Identity > One-line purpose]

## About
[Problem from Blueprint.md Section 1. Who uses it from Blueprint.md Section 2. Why now.]

## Features
[List only features that are in the MVP scope in PRD.md — mark P0 and P1 only]

## Tech Stack
[Exact stack from Technical_Requirements.md with version numbers]

## Prerequisites
[From Technical_Requirements.md Section 1 — exact version minimums]

## Installation
[Exact local setup steps from Technical_Requirements.md Section 5 > Local Setup]

## Configuration
[Every env var — cross-reference Security_Requirements.md to ensure secrets are documented
as placeholders only, never real values]

## Usage
[Walkthrough per user role from Blueprint.md Section 2, using screen flows from Screens.md]

## API Documentation
[Derived from API_Spec.md — summary table + auth flow + key endpoints]

## Project Structure
[Folder/module tree from Technical_Requirements.md Section 4]

## Testing
[Test commands and what they cover — from Testing_Strategy.md Appendix]

## Deployment
[From Technical_Requirements.md Section 5 > Build Pipeline and Environments]

## Security
[High-level summary only — do not expose threat model details. Reference that security
was designed per a formal threat model.]

## License
[From Blueprint.md Constraints if specified, else MIT]
```

---

## PHASE 4: SUPPORTING FILES

Create these files, all cross-referenced against /documents/:

**.env.example** — every variable used in the project, values replaced with descriptive placeholders.
Cross-reference Security_Requirements.md to catch any secret that might have been missed.

**.gitignore** — must include .env, any files flagged as containing RESTRICTED data in Security_Requirements.md.

**CONTRIBUTING.md** — include:
- Stack setup (from Technical_Requirements.md)
- Git workflow (from Technical_Requirements.md Section 4)
- Commit message format (from Technical_Requirements.md Section 4)
- PR requirements (from Technical_Requirements.md Section 4)
- Code style guide reference (from Technical_Requirements.md Section 4)

---

## PHASE 5: PERFORMANCE & UX POLISH

Cross-reference against:
- NFRs in SRS.md (NFR-Performance-[X]) — verify measurable targets are met
- Screens.md state variations — Loading / Empty / Error states present on every screen
- Design.md UX Patterns — form validation, error handling, empty states follow spec
- API_Spec.md rate limits — confirmed enforced

---

## PHASE 6: FINAL SPEC SIGN-OFF

Run the compliance audit from Phase 0 again as a final check.
Every item must pass. If anything still fails, fix it before outputting the final report.

---

## FINAL REPORT FORMAT

```markdown
# 🎉 Project Finalization Report

## Overview
**Project:** [from Blueprint.md]
**Status:** Production Ready ✅
**Date:** YYYY-MM-DD

---

## Spec Compliance Summary
| Document | Requirements | Satisfied | Gaps Remaining |
|---|---|---|---|
| PRD.md | [N] features | [N] ✅ | 0 |
| SRS.md | [N] FRs | [N] ✅ | 0 |
| Security_Requirements.md | [N] MUSTs | [N] ✅ | 0 |
| Permissions_Matrix.md | [N] permissions | [N] ✅ | 0 |
| API_Spec.md | [N] endpoints | [N] ✅ | 0 |
| Testing_Strategy.md | [N] test cases | [N] ✅ | 0 |

---

## Bug Scan Results
| Severity | Found | Fixed |
|---|---|---|
| Critical | X | X ✅ |
| High | X | X ✅ |
| Medium | X | X ✅ |
| Low | X | X ✅ |

---

## Documentation Created
✅ README.md — [X lines, covers all MVP features]
✅ .env.example — [X variables documented]
✅ .gitignore — [sensitive files excluded]
✅ CONTRIBUTING.md — [stack, git workflow, PR process]
✅ CHANGELOG.md — [X entries, all tasks documented]

---

## Spec Deviations (Total from all sessions)
[List any ⚠️ SPEC DEVIATION flags from CHANGELOG.md]
[If none: "No deviations — implementation matches specification exactly."]

---

## Final Checklist
**Code:** ✅ No console.logs | ✅ No hardcoded values | ✅ Error handling complete
**Spec:** ✅ All PRD P0+P1 features delivered | ✅ All SRS MUSTs satisfied
**Security:** ✅ All MUST controls implemented | ✅ No secrets in code
**Tests:** ✅ All IT/ST/UAT cases passing
**Docs:** ✅ README complete | ✅ API documented | ✅ Setup tested

---

## 🚀 Ready to Deploy
Follow deployment instructions in README.md > Deployment.
Reference Technical_Requirements.md Section 5 for environment configuration.
```

---

## CRITICAL RULES

1. README must only describe features that exist in PRD.md MVP scope — never add or remove
2. .env.example must cover every secret mentioned in Security_Requirements.md
3. Every bug fix must reference which spec document requirement it violates
4. The final compliance table must show 0 gaps remaining before marking production-ready
5. Spec Deviations list must be complete — pull every ⚠️ SPEC DEVIATION from CHANGELOG.md
```

---

## Quick Reference

```
PROMPT 1  →  Run once, after all 13 /documents/ files are locked
             Reads all 13 docs → creates /tasks/ structure → creates CHANGELOG.md

PROMPT 2  →  Run every work session
             Reads CHANGELOG → tasks.json → task file → referenced /documents/ sections
             Always traces work back to spec IDs (F-X, FR-X, SCR-X, IT-X, etc.)

PROMPT 3  →  Run once, after all tasks complete
             Audits against all 13 /documents/ → fixes bugs → writes README + supporting docs

/documents/  →  READ ONLY during development. Never modified by Prompts 1, 2, or 3.
/tasks/      →  Written by Prompt 1. Updated continuously by Prompt 2.
CHANGELOG.md →  Written by Prompt 1. Updated after every task in Prompt 2. Final update in Prompt 3.
```
