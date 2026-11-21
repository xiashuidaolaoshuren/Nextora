# AGENTS.md

Orientation for AI coding agents and new contributors. Keep it short; link to documents instead of copying them.

## What this repo is

**Nextora** (working name; some documents say "AI Career Copilot") is a local-first job-search workspace, initially for Hong Kong. A candidate imports a CV, confirms their evidence, pastes a job description, sees advisory eligibility flags and an evidence-based fit mapping, and tracks applications.

The first user is the author, working solo over about six months. The design keeps other users possible later without restructuring.

## Current status (2026-11-20)

**Documentation and design only. There is no application code yet.**

Done: competitive research, workspace core spec, domain language, six ADRs, roadmap, UI brief and a clickable design prototype.
Next: an implementation plan for the workspace core (`/writing-plans` on the spec), then the build.

## Where things are

| Path | Contents |
|---|---|
| `CONTEXT.md` | Domain language (Candidate, Job, Application, Evidence Item, Evidence Fit, and so on). Use these terms exactly. |
| `docs/superpowers/specs/2026-11-11-workspace-core-design.md` | **Source of truth for behavior** of sub-project 1, the workspace core. |
| `docs/adr/` | Architecture decisions 0001 to 0006. |
| `docs/roadmap.md` | Sub-projects 1 to 7, their order and decision gates. |
| `docs/design/ui-brief.md` | Look, layout, state wording and per-screen state matrix. Foundations plus per-sub-project screens. |
| `docs/design/prototype/` | Generated clickable prototype (`workspace-core.html`). **Git-ignored**, local only; regenerate it from the UI brief if missing. |
| `docs/research/` | Competitive research and the LuJie CareerKit UI digest. Background, not requirements. |

## Planned stack (from the core spec, nothing installed yet)

- Backend: Java (current LTS), Spring Boot, a modular monolith, PostgreSQL, Flyway, REST with an OpenAPI description.
- Frontend: React, TypeScript, Tailwind CSS, shadcn/ui, with an API client generated from the OpenAPI description.
- AI: a provider-agnostic gateway, Claude by default. It is the only code that calls a model.
- Local deployment with Docker Compose; hosted deployment is later.
- Several versions and library choices are deliberately unresolved: see section 14 of the spec. Do not assume them.

## Rules that must not be broken

These come from the spec and ADRs. If a change seems to need breaking one, stop and ask.

1. **Eligibility is advisory and separate from preferences.** Unknown is never guessed; missing facts or unrecognized conditions are `unknown`. Never infer Work Permission or Sponsorship to Start from residency or visa status. (ADR-0003)
2. **No fit score, band or percentage.** Fit is a requirement-by-requirement mapping to confirmed evidence. Invalid citations are "unable to assess", never "none". (ADR-0004)
3. **Only confirmed evidence is used in matching.** Evidence belongs to the candidate, not to a CV. (ADR-0002)
4. **Provider calls need an explicit user action.** Uploads, pastes and edits never call a model. Each action says what is sent and to whom. (ADR-0005)
5. **Jobs and Applications are separate records.** One job can have many attempts. (ADR-0001)
6. **Every user-owned record carries `user_id`**, resolved through one `CurrentUser` seam. Cross-candidate references fail as if the record did not exist.
7. **Original job text and CV files are stored unchanged** until the user deletes them.
8. **Every action is a backend operation**, reachable through the public API, because an agent will be added later (spec section 15.1).

## How to work here

- **Process:** brainstorm, then spec, then plan, then implement. Do not start building a sub-project without an approved spec and plan.
- **Precedence:** the spec defines behavior. The UI brief and prototype define appearance. If they disagree, the spec wins and the other is fixed.
- **Tests:** new or changed behavior is test-first (spec section 12). Eligibility rules, quote verification, ownership, status history and deletion recovery are the deterministic core and need tests before code.
- **Docs style:** plain, specific English. No unverified claims; label unknowns as unresolved.
- **Commits:** Conventional Commits (`docs:`, `feat(scope):`, `fix:`). Commit only when asked. Do not push unless asked.
- **Line endings:** files are written with LF; Git on Windows warns about CRLF conversion. This is harmless.

## Things not to do

- Do not copy code, images or text from reference products (LuJie CareerKit, JobsFlow, HK Job Radar). Use them for ideas only; licenses are unchecked.
- Do not scrape job boards or add automatic fetching of pasted source links.
- Do not treat the prototype as production code. The real frontend is built later from the approved design.
- Do not put job or CV text in application logs.
