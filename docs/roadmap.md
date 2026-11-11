# AI Career Copilot: Roadmap

**Last updated:** 2026-11-11
**Planning horizon:** about 6 months, solo
**Status:** Planning hypothesis, not a commitment. Revise after each sub-project.

Background: [competitive research](../AI_Career_Copilot_Competitive_Research.md) (Sections 5.4, 6, 7A). The research document's original 12-month phasing is superseded by this roadmap.

Each sub-project gets its own spec, then plan, then implementation. Week ranges are rough estimates and assume part of the time goes to review, testing and rework.

## Overview

| # | Sub-project | Weeks (approx.) | Depends on | Status |
|---|---|---|---|---|
| 1 | Workspace core | 1-8 | none | Spec written ([spec](superpowers/specs/2026-11-11-workspace-core-design.md)) |
| 2 | Materials | 9-13 | 1 | Not started |
| 3 | Capture and discovery | 14-19 | 1 | Not started |
| 4 | Autofill (human-in-the-loop) | 20-23 | 1, 2, 3 | Not started |
| 5 | Interview preparation | 24-26 | 1, 2 | Not started |
| 6 | Hosted deployment | after 5, or earlier if needed | 1 | Not started |
| 7 | AI agent | late, optional | 1 and any of 2-5 | Not started |

Sub-projects 2 and 3 are independent of each other once the core exists and can swap order. Sub-project 5 has no dependency on 3 or 4 and can be moved earlier if interview preparation matters more to you than capture.

## 2. Materials

**Goal:** Tailored CV and cover letter for a specific job, built only from confirmed evidence.

**Scope**
- Bounded CV edits: the model proposes replace, reorder or append operations on a chosen base CV, each linked to evidence items.
- Cover-letter drafting grounded in the job and confirmed evidence.
- Claims beyond the evidence require explicit user confirmation.
- A separate audit pass checks the output against the job's requirements and the evidence.
- CV comparison between versions, editable output, DOCX and PDF export.
- Screening-answer preparation grounded in confirmed evidence.
- Capture of user corrections as reviewable preferences, never promoted silently.

**Exit criterion:** Generated material is editable, every claim traces to confirmed evidence, and none invents candidate facts.

**Open questions**
- How to render and export CVs (template approach, library choice).
- Whether CV layout formatting is in scope or only content.
- How to judge quality; plan a small evaluation set.

## 3. Capture and discovery

**Goal:** Remove copy-paste for restricted boards and find jobs from permitted sources.

**Scope**
- **Browser extension:** the user views a job on a board, clicks once, and the job text and URL go to the workspace. The extension must be reviewed per site before it is enabled for that site.
- **Tier 1 sources** (see research Section 5.4): Workday-hosted career sites with public job endpoints, company career pages, and other public ATS interfaces where terms permit.
- Source adapter abstraction with provenance, retrieval time and per-source policy.
- Cross-source deduplication on normalized company and title.
- Liveness check before a job is shown as open.
- Digest and alerts (email first; Telegram or WhatsApp optional) with a score or band threshold for instant alerts, plus thumbs up and down feedback.

**Decision gate before building:** a per-source terms review for JobsDB, LinkedIn, Indeed, CTgoodjobs and OfferToday. The outcome sets which sites the extension supports. This is a product review, not legal advice; get professional advice before any commercial launch.

**Exit criterion:** Several source types feed the same job intake flow, and a failed or disallowed source falls back to pasting without breaking anything.

**Open questions**
- Extension browsers to support (Chrome first).
- How capture authenticates to the local or hosted backend.
- Scheduling for discovery runs.

## 4. Autofill (human-in-the-loop)

**Goal:** Prepare application forms from confirmed data; the user reviews and submits.

**Scope**
- Fill common fields and screening answers from the profile, evidence and prepared answers.
- Stop before submission; the user always clicks submit.
- Every fill action is observable: what was filled, from which source, with a confidence flag.
- Respect per-site permissions from the sub-project 3 review.

**Exit criterion:** On supported sites, the user can complete an application from a prepared workspace and see exactly what was filled and why.

**Open questions**
- Which sites and forms are in scope; ATS forms vary widely.
- Handling of declarations the user must answer themselves.

## 5. Interview preparation

**Goal:** Preparation that reuses the job, the CV version and the application materials.

**Scope**
- Interview plan generated from the specific job and the CV version used.
- Behavioral and technical question practice with saved answers.
- Feedback and knowledge-gap notes, stored with the application.
- Confirmed learning updates used in later preparation.
- AI mock interview in text; voice or video is out of scope for this roadmap.

**Exit criterion:** An application can move from submitted to a context-aware interview workflow without re-entering context.

**Open questions**
- How feedback quality is judged.
- Whether answers feed back into the evidence library, and how the user confirms that.

## 6. Hosted deployment

**Goal:** Let other people use the product without technical setup.

**Scope**
- Real sign-in replacing the `CurrentUser` seam.
- Per-user data isolation checks, backups, export and deletion.
- Privacy statement covering model-provider data flow.
- Cost tracking per user and model usage limits.

**Decision gate:** whether to host at all, based on the author's own use and whether others want it. Billing is not planned.

**Open questions**
- Hosting platform and cost.
- Data-protection duties for personal data of other users, including CV contents. Get advice before inviting users.

## 7. AI agent

**Goal:** Permission-aware automation for monitoring, reminders and multi-step preparation.

**Constraints** are in [spec section 15.1](superpowers/specs/2026-11-11-workspace-core-design.md): reaches the backend only through its public API or MCP tools, approval checks live in the backend, and its language is decided at that time.

**Likely scope:** scheduled discovery checks, reminders for deadlines and follow-ups, and multi-step "prepare this application" runs, each with task status, failure reason and an approval step.

**Prerequisite:** sub-projects 1 to 3 working reliably.

## Cross-cutting items

These are not sub-projects but need attention along the way.

- **Evaluation set:** a small, maintained set of real or representative job descriptions and CVs to track extraction and matching quality as models and prompts change.
- **Cost control:** log and review model cost per task; consider a cheaper model for extraction.
- **Security:** API key handling, file upload validation, prompt-injection handling of ingested text.
- **Backup and restore** and data export, ahead of any hosted use.
- **Chinese interface:** deferred. Chinese job and CV text is handled from sub-project 1; a translated interface is a later addition if other users need it.

## Parked ideas

Not planned; keep until there is evidence they matter.

- Voice or video mock interviews.
- Large-scale central scraping of restricted boards (excluded by the research findings).
- Fully automatic application submission (excluded by design).
- Salary benchmarking and offer comparison.
- Greater Bay Area expansion beyond Hong Kong (sources, work-rights rules and languages differ).
- Public hkjob-style guides or content as an acquisition channel.

## Review points

Revisit this roadmap after each sub-project:

1. Did the earlier estimate hold? Adjust weeks for the rest.
2. Did real use change which sub-project matters next?
3. Did any source or platform terms change?
