# Competitive Research: AI Career Copilot

**Prepared:** 2026-10-02  
**Last updated:** 2026-11-11 (added hkjob.app, JobsFlow, and HK Job Radar; revised source strategy and MVP scope)  
**Purpose:** Product discovery and architecture reference for an AI-powered job-hunting platform initially focused on Hong Kong and the Greater Bay Area (GBA).

> **Research note:** This document synthesizes publicly presented product information and the supplied product links. Feature descriptions are based on public websites or repository documentation, not independent product testing. Marketing claims and stated outcomes should not be treated as independently verified. Terms of service, API access, licensing, and product capabilities can change; verify current official documentation before implementation or launch.

---

## 1. Executive Summary

Six products illustrate complementary approaches to job hunting:

- **JobCopilot — automation-first:** Reduce repetitive job-search and application work through automated job discovery, tailored application materials, application assistance, and tracking.
- **HoiSum — matching and delivery-first:** Filter job opportunities against a candidate profile and deliver curated opportunities with job-specific application materials.
- **LuJie CareerKit — workspace-first:** Organize resumes, job-description analysis, application tracking, interview preparation, and related career artifacts in one workspace.
- **hkjob.app — hosted catalogue and content-first:** A Hong Kong job catalogue with CV-based matching and a large body of local job-search guides.
- **JobsFlow — local, gated pipeline:** An open-source, local-first Python pipeline with two-stage scoring, bounded CV tailoring, an independent audit, and human confirmation gates.
- **HK Job Radar — local daily digest:** An open-source, local tool that scores jobs with an LLM and pushes a daily digest, with semi-automatic form filling that stops before submit.

**Key finding from the second research round (2026-11-11):** both open-source Hong Kong tools reach JobsDB and LinkedIn only from the user's own machine, using the user's own browser session and manual Cloudflare/login verification. Neither documents server-side aggregation of those boards. A hosted product therefore cannot assume it may copy that approach. Server-side discovery should be limited to permissive sources, and restricted boards should be reached through user-initiated capture (see Section 5).

The opportunity for **AI Career Copilot** is not simply to combine every feature. It is to connect three layers around a persistent, user-controlled career profile:

1. **Career Memory:** A structured, versioned source of truth for a candidate's experience, skills, preferences, applications, and interview learnings.
2. **Career Intelligence:** Job parsing, evidence-based matching, skill-gap analysis, prioritization, and job-specific application material preparation.
3. **Career Agent:** Permission-aware automation for monitoring, reminders, preparation, form assistance, and interview workflows, with human approval for consequential actions.

The initial product should focus on a useful, reliable end-to-end workflow rather than attempting large-scale job-board scraping or fully automated applications from day one.

## 2. Product Profiles

### 2.1 JobCopilot

**Positioning:** AI-assisted and automated job search/application workflow.

**Publicly presented capabilities** (verify against current official product pages):

- Configure target roles and job-search preferences.
- Discover matching opportunities.
- Use a candidate's resume and preferences to tailor application materials.
- Assist with or automate application form completion and submissions.
- Track application activity.
- Reuse or remember answers and user edits for subsequent applications.

**Official reference:** [JobCopilot](https://jobcopilot.com/)

#### What is worth learning from it

**A. Configure once, reuse continuously**

Collect a candidate profile, target roles, constraints, and reusable application answers up front. Reuse these settings across future jobs instead of asking the user to repeat them.

**B. Treat feedback as structured memory**

When the user edits an AI-generated answer, consider storing the confirmed preference or correction as structured data. Do not assume every edit is a universal preference; allow the user to review, correct, and remove remembered information.

**C. Make automation configurable**

Allow users to set job filters, excluded conditions, and approval requirements. Distinguish actions that can run automatically from those requiring confirmation.

**D. Make execution observable**

Every automated task should have a status, timestamp, result, failure reason, and recovery path. Users should be able to inspect and take over a workflow rather than trust a black box.

#### Risks and design implications

- Application volume is not equivalent to application quality.
- Incorrect or invented answers can harm the candidate.
- Automated access, form filling, and submission may be restricted by individual job platforms.
- Final submission and important declarations should remain under explicit user control unless the relevant integration and workflow are authorized and appropriately designed.

**Product takeaway:** Borrow the idea of persistent, configurable automation, but implement it incrementally and transparently.

---

### 2.2 HoiSum AI 搵工

**Positioning:** Curated job matching and tailored application-material delivery, with an initial Hong Kong job-market focus.

**Publicly presented capabilities** include:

- Using candidate information to infer job preferences and asking follow-up questions when important information is missing.
- Reviewing public job opportunities and filtering for candidate fit.
- Preparing job-specific resumes and cover letters.
- Providing a private job library and progress/deadline-related tracking.
- Describing its sources as public employer career pages, public announcements, and partner channels.
- Stating that it does not log into job platforms, use login-gated content, or submit applications on the user's behalf.

**Official reference:** [HoiSum](https://hoisum.hk/)

#### What is worth learning from it

**A. Sell an outcome, not just a text-generation tool**

A candidate needs to know which roles merit attention, why they fit, what evidence supports the match, and what to do next. A match score alone is insufficient.

For each job, the product should answer:

- Why may this job fit the candidate?
- Which resume experiences support the match?
- Which requirements lack supporting evidence?
- What should be emphasized in the application?
- What is the next action and any relevant deadline?

**B. Minimize repeated data entry**

Parse existing resumes and profile information first. Ask only for missing or ambiguous details that materially affect matching or application quality.

**C. Keep application materials job-specific**

Create a distinct application workspace for each opportunity. Preserve the original job description/reference, resume version used, tailored resume, cover letter, screening answers, status, and later interview notes.

**D. Treat source restrictions as a product-design input**

The source strategy should be modular. The product must not depend entirely on copying content from a particular job board. Publicly accessible content is not automatically free of restrictions on copying, storage, aggregation, or redistribution.

#### Business-model insight

HoiSum publicly presents trial and paid service options, including one-off and recurring offerings. This illustrates different demand patterns: a one-time application-material need versus ongoing job-search support. Pricing and packaging should be validated with users rather than copied directly.

#### Important uncertainty

Public product descriptions do not establish whether HoiSum has authorization from every platform, nor do they reveal all implementation details of any browser extension or import flow. Do not treat its implementation as proof of legal or technical compliance.

**Product takeaway:** Borrow the curated, job-specific workflow and low-friction onboarding—not unverified assumptions about how job sources may be accessed.

---

### 2.3 LuJie CareerKit

**Positioning:** An open-source, AI-powered career workspace covering the job search through interview preparation.

**Public repository:** [Chozzc/Lujie-Careerkit on GitHub](https://github.com/Chozzc/Lujie-Careerkit)

The repository documentation describes capabilities such as:

- Resume management and multiple resume versions.
- Job-description matching and resume optimization.
- Cover-letter generation.
- Application tracking.
- Job-specific interview preparation.
- Mock interviews and AI feedback/review.
- Local SQLite-based storage.
- Agent Skills/workflows intended for use with coding agents such as Codex and Claude Code.

These descriptions reflect repository documentation; validate the current code and license before reusing any implementation.

#### What is worth learning from it

**A. Version resumes instead of overwriting them**

Preserve the original resume and create a new version for each meaningful revision or application. Support diffing, history, restoration, and provenance.

**B. Diagnose before rewriting**

Analyze a resume against a JD, explain the gaps, and let the user choose what to change. Avoid inventing skills, experience, qualifications, metrics, or achievements merely to increase apparent fit.

**C. Reuse application context for interview preparation**

Interview preparation should use the exact job description, resume version, and application materials associated with that application. Do not make the user reconstruct the context after receiving an interview invitation.

**D. Consider local-first or self-hosted deployment**

Local storage can offer users more direct control over career data and can be attractive for an open-source project. It does not by itself guarantee privacy: model-provider data handling, API-key security, backups, telemetry, and deletion still require careful design.

**E. Model AI workflows as explicit modules**

A workflow should have defined inputs, outputs, validation, and constraints instead of relying on one large prompt. Agent Skills can be useful interfaces for reusable tasks, but they should be tested and versioned like other software.

**Product takeaway:** Borrow the unified workspace, versioning, and reusable workflow approach. Differentiate through reliable shared context, evidence traceability, and permission-aware agents.

---

### 2.4 hkjob.app

**Positioning:** A hosted Hong Kong job-search site with CV-based matching and extensive local guides.

**Official reference:** [hkjob.app](https://hkjob.app/)

**Evidence base and limits:** Based on three pages: the homepage (a JavaScript shell with almost no readable content), `/jobs`, and `/zh-Hant/guides`. Pricing, operator, privacy/terms, and any app or extension were not found. Treat this profile as partial.

**Publicly presented (stated on the pages):**

- Headline: 「上傳履歷，探索與你配對的職位」 (upload your CV and explore matched jobs), with the sub-line 「看清職位符合什麼、還差什麼」 (see what a job matches and what you are still missing).
- Job catalogue of about 1,060 listings (page 1 of 22), with category collections (full-time, part-time, software engineering, data and AI, finance, product, operations) and district collections (Central, Mong Kok, Kowloon City).
- Per-listing status labels: 「已核實」 (verified) or 「狀態待核實」 (pending verification); salary shown as an HKD range or 「薪酬未公開」 (not disclosed); a filter for jobs with disclosed pay.
- Trilingual interface: Traditional Chinese, Simplified Chinese, English.
- A guides hub covering job search, CVs, interviews, offers, contracts, statutory holidays, MPF, government job applications, Cantonese/English/Mandarin language requirements, IANG and GEP visas, fresh-graduate search, and recruitment-scam awareness.

**Not stated:** Where jobs come from or how they are collected; AI matching details; CV or cover-letter generation; autofill; tracking; alerts; pricing; operator.

#### What is worth learning from it

**A. Frame matching as "what fits, what is missing."** This matches the evidence-based match explanation proposed in Section 4.2.

**B. Make data quality visible.** Verified/unverified labels and an explicit "salary not disclosed" state are cheap trust signals.

**C. Use local guides as an acquisition channel.** Content on IANG, MPF, language requirements, and scams addresses real Hong Kong questions and can bring in users searching for them. This is an inference about strategy; no traction data was found.

**D. Treat a bare catalogue as weak.** About 1,060 listings cannot compete on coverage with the major boards. Differentiation has to come from matching, workflow, and trust rather than listing volume.

**Product takeaway:** Borrow the gap-focused framing, data-quality labels, and local content strategy. Do not compete on catalogue size.

---

### 2.5 JobsFlow (mixxmax/jobsflow)

**Positioning:** A local-first, Hong Kong-oriented job-search execution system driven through an AI coding assistant. It searches, scores, prepares materials, and records applications, but never submits.

**Repository:** [mixxmax/jobsflow](https://github.com/mixxmax/jobsflow). MIT license (README states it keeps the upstream MIT notice from MadsLorentzen/ai-job-search). At the time of research: 7 stars, 1 fork, 172 commits on `main`, last commit 2026-11-09. README is in Traditional Chinese, with English and Simplified Chinese versions listed but not read. Files under `docs/` (system rules, JobsDB handbook, scoring details, prompts) were not read.

**Documented capabilities:**

- Setup from an existing CV folder, producing a private profile, search configuration, scoring weights, and tracker columns.
- Scan modes (incremental, 3-hour, 24-hour) and depth presets (about 10, 20, or 40 deep fetches).
- Two-stage scoring: a preliminary score decides fetch priority; a deep score runs on the full job description. Dimensions: resume match, eligibility feasibility, direction, industry, working hours, salary.
- `/intake` accepts a job URL or pasted JD and returns a read-only proposal; `/push` writes to a local CSV ledger or optional Google Sheets only after confirmation.
- `/materials` builds a tailored CV and cover letter from per-lane base versions, runs an independent audit, renders DOCX/PDF through LibreOffice, then applies a format gate.
- `/apply` checks completeness; the user submits.
- 60-day JD cache; company research with source tracking; separation of the job poster (recruiter or employer) from the actual hiring company; preflight extraction of salary, start date, work rights, language, and attachment requirements.
- A confirmation-gate layer ("SOP Control") with an audit log.

**Sources and acquisition (as stated):** LinkedIn (search and detail via an unnamed "LinkedIn CLI"); JobsDB (cache first, then a request through the user's own visible Chrome over CDP, with a one-time manual Cloudflare verification, never headless and never copied cookies); CTgoodjobs (structured or cached path); FreeHire (by job identifier). Playwright is an optional deep-JD fallback. The README recommends pasting the full JD for JobsDB, CTgoodjobs, and FreeHire. Repeated challenges trip a circuit breaker and mark the job as needing a paste. No official APIs, RSS, or browser extension are mentioned.

**Stack:** Python 3.10+, local files (CSV, JSON, DOCX/PDF), no database, no scheduler (runs on demand), local source checkout only. No model or provider is named; optional external LLM use is configured by environment variables and the README warns that data is sent to that provider. License and activity data above.

**Stated limits and disclaimers:** Use of recruitment sites may be subject to their terms of service and the user should assess this; no legal, compliance, or immigration advice; not multi-user SaaS; WPS and GUI automation unsupported; the ATS-related metrics are internal engineering metrics, not official ATS scores.

#### What is worth learning from it

**A. Two-stage scoring with deterministic rules after the model.** Cheap pre-filter first, deep scoring only on survivors, then code applies caps, eligibility checks, and tiers. Semantic fallback is capped when the model step is incomplete.

**B. Bounded CV tailoring.** The model proposes a structured plan of `replace` / `reorder` / `append_after` operations on a confirmed base CV, tied to evidence IDs with allowed and prohibited claims; wording beyond the evidence needs explicit confirmation. This is the most concrete mechanism found for the "no invented facts" requirement in Section 7.1.

**C. Independent audit pass.** A separate check of the output against the JD mapping, with a call limit and circuit breaker.

**D. Confirmation gates at every consequential step.** Profile, base CV, push to tracker, claims beyond evidence, and submission each require confirmation. The README itself notes that a confirmation receipt does not prove a human is present.

**E. Treat failure as a normal state.** Blocked fetches degrade to "paste the JD" instead of failing the workflow.

**Limits as a reference:** Single-maintainer project at an early stage; requires Python, LibreOffice, and an AI assistant; local only. Useful as a design reference, not as a product for non-technical users.

---

### 2.6 HK Job Radar (prui0474/hk-job-radar)

**Positioning:** A free, local-running Hong Kong job-search automation tool. It describes itself as "not a job board, not a recruiter, and not a resume writer."

**Repository:** [prui0474/hk-job-radar](https://github.com/prui0474/hk-job-radar). MIT license. At the time of research: 82 stars, 5 forks, 27 commits on `master`, first commit 2026-10-14, last commit 2026-10-23, 1 open issue, no releases. The raw README URL on `main` returned 404; content came from the rendered repo page, and some architecture details come from a model-generated summary and are unverified.

**Documented capabilities:**

- Four toggleable sources: JobsDB, LinkedIn, Indeed, and company career sites on Workday (five built-in employers; users can add more).
- Keyword and synonym pre-filter, then LLM scoring (DeepSeek by default, OpenAI-compatible) of 0–100 with a one-line reason. Score 60 or above enters the daily digest; 80 or above triggers an instant push.
- Eligibility notes read from the ad (for example permanent-resident only, Cantonese required), shown as notes only with no score deduction.
- Push channels: WeCom, WxPusher, email, Telegram (with feedback buttons).
- Cross-board deduplication on normalized company and title; local SQLite with job ID plus normalized URL.
- Local workbench, trend board (weekly posting counts, salary bands), and application board with applied/interview/closed tracking.
- Semi-automatic application: fills forms and screening answers and stops before submit.
- Optional resume-versus-JD gap analysis and interview question preparation.
- Liveness check (opens the job page before pushing), anti-ban measures (random delays, per-site daily page caps, exponential backoff), and prompt-injection hardening of job-ad text.
- Visa-aware default: `career_stage: student` limits results to internships and part-time roles; the README suggests switching after obtaining IANG.

**Sources and acquisition (as stated):** Playwright with Chromium. LinkedIn needs a one-time login by the user. JobsDB and Indeed need one human-verification pass in a browser; the tool says it does not bypass Cloudflare or CAPTCHA. Workday sites are read through each employer's own public job API. No CTgoodjobs, OfferToday, government sites, Greenhouse, or Lever.

**Stack:** Python 3.10+, Playwright, YAML config, SQLite, Windows `.bat` and macOS `.command` launchers, local only, daily schedule via the open start window (Windows) or a registered daily job (macOS).

**Stated limits:** Technical setup needed; third-party sites may rate-limit or block; LinkedIn may restrict logins from other regions; coverage is not complete; keywords are the user's responsibility. The README has no terms-of-service warning for JobsDB, Indeed, or LinkedIn, only a compliance note that it has no automatic submit path and does not solve CAPTCHAs. Its claim that data does not leave the machine is qualified, because job text and the user profile are sent to the DeepSeek API.

#### What is worth learning from it

**A. A visa-aware stage is a real segment.** Non-local students on a student visa and IANG graduates have different eligible roles; a default that respects this is a differentiator for Hong Kong.

**B. Cheap filter before the LLM.** Keyword and synonym filtering keeps model cost down.

**C. Delivery matters.** A daily digest plus instant alerts for top matches, with feedback buttons, is a low-friction habit loop.

**D. Operational hygiene.** Liveness checks, cross-board deduplication, and prompt-injection stripping on scraped text are all worth building.

**E. Workday public endpoints are a practical employer-site route.** Many Hong Kong banks and insurers use Workday.

**Limits as a reference:** Eligibility and language signals are mostly left to the LLM reading free text rather than structured fields; there is no documented salary, district, or fresh-graduate filter; scraping approach and its terms-of-service exposure are not discussed; roughly three weeks old with a single author.

---

## 3. Comparative Overview

| Dimension | JobCopilot | HoiSum | LuJie CareerKit |
|---|---|---|---|
| Main product emphasis | Automation and execution | Curated matching and delivery | Personal career workspace |
| Job discovery | Automated discovery is a key proposition | Public-source monitoring and curation | Can begin with user-provided job descriptions; verify current implementation |
| Job-specific resume | Presented as a feature | Core part of the service | Resume optimization/versioning workflow |
| Cover letters | Presented as a feature | Core part of the service | Presented as a feature |
| Application assistance/submission | Automation-oriented | Publicly states it does not submit applications | Not the primary differentiator in its public description |
| Application tracking | Presented as a feature | Job library/progress management | Presented as a feature |
| Interview preparation | AI interview-related capabilities are presented | Less central to public positioning | Preparation, mock interviews, and review |
| Persistent candidate context | Reuse of candidate data and answers | Profile-based preference inference | Stored career artifacts and application context |
| Open-source status | Not verified in this research | Not verified in this research | Public GitHub repository; verify the current license before reuse |

**Interpretation:** These are differences in publicly emphasized positioning, not proof that a product lacks an unlisted capability.

### 3.1 Additional products (second research round)

| Dimension | hkjob.app | JobsFlow | HK Job Radar |
|---|---|---|---|
| Form factor | Hosted website, trilingual | Local Python, driven through an AI coding assistant | Local Python, daily digest |
| Job sources | Unnamed "different sources"; about 1,060 jobs | LinkedIn, JobsDB, CTgoodjobs, FreeHire | JobsDB, LinkedIn, Indeed, Workday career sites |
| Acquisition method | Not stated | User's own Chrome (CDP), CLI tools, paste fallback | Playwright with user login; Workday public API |
| Matching | CV upload; match and gap view (details not stated) | Two-stage scoring, six dimensions, deterministic caps | Keyword pre-filter, then LLM 0–100 score with reason |
| CV and cover letter | Not stated | Bounded edits with evidence IDs, audit, DOCX/PDF | Optional gap analysis only |
| Application submission | Not stated | Never submits | Fills forms, stops before submit |
| Tracking | Not stated | Local CSV or Google Sheets ledger | Local application board |
| Interview preparation | Guides only | Not documented | Optional question prep |
| HK-specific | District collections, language/visa/MPF guides | HK sources; poster vs. employer separation | Visa-aware career stage; Cantonese/PR notes |
| Pricing | Not found | Free, MIT | Free, MIT |
| Maturity signal | Unknown operator | 7 stars, 172 commits, active | 82 stars, 27 commits, last commit 2026-10-23 |

Star counts and commit dates were read on 2026-11-11 and will change.

### 3.2 Cross-cutting findings

1. **Restricted-board access happens on the user's machine.** Both open-source tools reach JobsDB and LinkedIn through the user's own browser session or login, with manual verification. This lowers but does not remove terms-of-service exposure (JobsFlow tells users to assess it themselves; HK Job Radar does not address it). A hosted service that fetched these boards centrally would be in a different position.
2. **Nobody serves the non-technical user.** The capable tools require Python, API keys, and a command line. The hosted one (hkjob.app) shows a catalogue and matching but no documented workflow.
3. **Eligibility is under-structured.** Language, district, visa, and fresh-graduate suitability are mostly left to the LLM reading free text; only hkjob.app shows structured district collections.
4. **Coverage gaps.** None of the three covers OfferToday; only JobsFlow covers CTgoodjobs; none documents government or civil-service jobs.
5. **The application record is the backbone.** The tools that work around a persistent record (ledger, application board) rather than a one-off match.
6. **Human gates are the norm.** None of the three submits an application automatically. Two explicitly prohibit it.

---

## 4. Proposed Product Concept: AI Career Copilot

### 4.1 Product goal

Build an AI-powered career workspace that helps job seekers manage the full job-hunting lifecycle—from job discovery and evaluation to tailored applications, tracking, and interview preparation—with an initial focus on Hong Kong and the Greater Bay Area.

The product should reduce fragmentation across job boards, resume files, AI tools, spreadsheets, and interview notes. It should preserve candidate-controlled career context over time and make AI actions transparent and reviewable.

### 4.2 Suggested architecture: three layers

#### Layer 1 — Career Memory

A user-controlled, structured, versioned source of truth for:

- Candidate profile and job preferences.
- Resume versions and project/experience records.
- Skills and evidence supporting each skill.
- Application-specific artifacts and statuses.
- Interview answers, feedback, and learning notes.
- User-approved preferences and corrections.

Distinguish confirmed facts, user preferences, temporary application context, and AI-generated inferences. Do not promote an inference to a fact without confirmation where accuracy matters.

#### Layer 2 — Career Intelligence

Capabilities include:

- Job-description parsing and normalization.
- Evidence-based candidate/job matching.
- Requirement-to-evidence mapping.
- Skill-gap analysis.
- Explainable prioritization.
- Job-specific resume and cover-letter drafting.
- Screening-answer preparation grounded in verified candidate information.

A match score should be explainable and should not imply false precision. Show supporting evidence, missing evidence, uncertainty, and relevant constraints.

#### Layer 3 — Career Agent

Capabilities to introduce progressively:

- Scheduled checks against authorized job sources.
- New-job alerts and reminders.
- Application-material preparation.
- Application form assistance where permitted.
- Interview preparation and mock interviews.
- Follow-up reminders and workflow orchestration.

Each task should be observable and recoverable. Keep explicit user approval for final submissions and consequential declarations. Respect each platform's terms, permissions, API rules, and technical limits.

### 4.3 Application Workspace

Treat each application as a first-class entity that connects all relevant context:

- Original job description or permitted reference to it.
- Source platform, original URL, and retrieval/import method.
- Job analysis and match explanation.
- Resume version used and tailored version.
- Cover letter and screening answers.
- Application status, dates, deadlines, and notes.
- Interview schedule, questions, practice answers, and feedback.
- User edits and decisions.

This workspace is the bridge between job matching, application preparation, and interview preparation.

---

## 5. Job-Source Strategy and Compliance Considerations

Job acquisition is a separate concern from AI career intelligence. Avoid coupling the whole application to one scraping method or job board.

### 5.1 Source adapter pattern

Use a common source abstraction with separate implementations and explicit capabilities, for example:

- Official API or authorized partner integration.
- Public ATS/job-board interface where its terms permit the intended use.
- User-initiated import or pasted job description, where the workflow is permitted.
- Manual entry or reference-only storage.

Normalize permitted data into a common internal job model. Keep provenance, timestamps, original links, and source-specific restrictions. Apply deduplication only after ingestion is authorized.

Example conceptual flow:

`Authorized Source / User Import → Source Adapter → Validation → Normalization → Deduplication → Job Store/Reference → Career Intelligence → Application Workspace`

### 5.2 Per-source policy

Do not assume that browser-extension access makes extraction compliant. User initiation, reading a visible page, DOM access, automated scanning, persistent storage, aggregation, and redistribution are distinct behaviors and may be treated differently by each platform.

For every source, document:

- Whether an official API or partnership exists and whether access is approved.
- Whether public job data is available.
- Whether authentication is required.
- Whether the intended extraction method is permitted.
- Whether data may be stored, transformed, displayed, or redistributed.
- Required attribution, link-back, retention, rate limits, and deletion behavior.
- Whether user-initiated import is permitted by current terms.
- Whether automated form filling or submission is allowed.

Treat LinkedIn, JobsDB, Indeed, OfferToday, CTgoodjobs, and other restricted platforms as separate legal/technical reviews—not as a single generic scraper target. Obtain professional legal advice before a commercial launch where appropriate. This document is product research, not legal advice.

### 5.3 Practical implementation principle

Do not make large-scale scraping of restricted platforms an MVP dependency. Begin with authorized/public interfaces and controlled import workflows. If a source later becomes unavailable or disallows a method, the AI and application-management layers should continue to work.

### 5.4 Source tiers (updated 2026-11-11)

Evidence from JobsFlow and HK Job Radar suggests organizing sources by who performs the access:

| Tier | Method | Examples | MVP role |
|---|---|---|---|
| 1. Permissive server-side | Official APIs, partner feeds, public employer endpoints | Workday-hosted career sites (public job endpoints, as used by HK Job Radar), company career pages, other public ATS interfaces where terms permit | Automatic discovery and scheduled checks, after a per-source terms review |
| 2. User-initiated capture | The user views a job and sends it to the workspace through a browser extension or paste | JobsDB, LinkedIn, Indeed, CTgoodjobs, OfferToday | Primary route for restricted boards, subject to each platform's current terms |
| 3. Manual entry | Pasted JD or URL reference only | Any source | Always-available fallback |

Design rules drawn from the reference tools:

- Every source adapter must degrade to "ask the user to paste the JD" rather than failing the workflow (JobsFlow's circuit-breaker pattern).
- Do not copy cookies, bypass CAPTCHA or Cloudflare, or run unattended logged-in scraping of restricted boards from a central server.
- Run a liveness check before alerting on a job, deduplicate across boards on normalized company and title, and strip hidden text and prompt-injection from ingested job ads before they reach the model.
- Record the separate "job poster" (recruiter or employer) and "hiring company".

This is a product-design hypothesis, not a legal conclusion. The tiering does not make Tier 2 compliant by itself: user initiation, DOM access, storage, and aggregation remain separate questions for each platform, as noted in Section 5.2. The reference tools' use of the user's own session is documented by them, not verified as compliant.

---

## 6. Recommended Roadmap

This is a planning hypothesis, not a fixed commitment. Adjust based on personal use, implementation capacity, and source-access findings.

### Phase 1 — Personal career workspace (Months 1–2)

- Candidate profile and structured experience library.
- Resume storage, versioning, and comparison.
- Manual JD paste/import.
- Basic JD parsing and evidence-based matching.
- Application tracker and per-application workspace.

**Exit criterion:** The user can manage a real application end-to-end without losing context.

### Phase 2 — Application-material agent (Months 3–4)

- Tailored resume and cover-letter drafting.
- Traceability from claims to confirmed candidate evidence.
- Editable outputs, version history, and diffing.
- Screening-answer preparation.
- Capture user-approved corrections and preferences.

**Exit criterion:** Generated material is useful, editable, and does not invent candidate facts.

### Phase 3 — Job discovery and browser entry point (Months 5–7)

- Integrate authorized public sources and official APIs where available.
- Common job schema, deduplication, provenance, and source policy.
- Investigate a Chrome extension as a user-facing entry point.
- Support appropriate manual import flows for sources that cannot be automatically ingested.
- Validate platform terms before enabling each source capability.

**Exit criterion:** Multiple source types can feed the same workflow without relying on prohibited scraping.

### Phase 4 — Interview and review agent (Months 8–10)

- Generate interview plans from the relevant JD and resume version.
- Support behavioral and technical question practice.
- Save answers, feedback, and knowledge gaps.
- Use confirmed learning updates in subsequent preparation.

**Exit criterion:** An application can progress from submitted materials to a context-aware interview workflow.

### Phase 5 — Automation, quality, and release (Months 11–12)

- Scheduled checks and notifications for authorized sources.
- Application form assistance and user-confirmed submission workflows where permitted.
- Reliability, privacy, security, and model-cost testing.
- Data export/deletion and backup/restore.
- Decide on open-source scope, deployment model, and whether to test a hosted offering.

**Exit criterion:** A documented, usable release with clear source boundaries and recoverable workflows.

---

## 7. Differentiation Opportunities

Do not rely on feature count alone. Potential differentiators for this project are:

### 7.1 Evidence-grounded career intelligence

Every important match or application claim should be traceable to the job requirement and a specific, user-confirmed candidate experience. Clearly mark missing evidence and uncertainty.

### 7.2 Persistent, user-controlled career memory

Store more than chat history. Maintain structured experiences, preferences, application context, and interview learning, with provenance, versioning, editing, and deletion controls.

### 7.3 A connected end-to-end workflow

Make job discovery, analysis, tailored materials, application tracking, and interview preparation use the same application context. Avoid isolated tools that repeatedly ask for the same inputs.

### 7.4 Hong Kong/GBA localization

Potential local factors include Cantonese, English, and Mandarin requirements; location and commuting constraints; work mode; fresh-graduate suitability; local resume conventions; and cross-border employment considerations. Validate these needs through actual use and research rather than assuming they apply to every user.

**Structured eligibility (from the second research round).** Competing tools mostly leave language, visa, and district signals to an LLM reading free text. Model them as structured fields extracted from each job and compared against the candidate's profile, and keep them separate from the fit score:

- Work-rights status (for example student visa, IANG, GEP, permanent resident) against job restrictions.
- Language requirements by language (Cantonese, English, Mandarin) and level.
- District and commute constraints, work mode, fresh-graduate suitability, and disclosed salary.

Treat eligibility as flags and gates, not as part of a single blended percentage. Non-local students and IANG graduates are a candidate first segment because HK Job Radar designs its defaults around them and hkjob.app publishes visa guides, but this is an inference from product design choices; no demand data was found.

### 7.5 Constrained, auditable generation

JobsFlow's approach is a concrete reference for Section 7.1: the model proposes bounded edits to a confirmed base CV, each tied to an evidence ID; claims beyond the evidence need explicit user confirmation; and a separate audit pass checks the result. Adopt the pattern, not the code.

### 7.6 Usable by non-technical users

The capable open-source tools need Python, API keys, and a command line. A hosted workspace with a browser extension that needs no setup is an unserved position among the products reviewed. The trade-off is that a hosted service takes on privacy, security, and data-handling duties that local tools avoid.

### 7.7 Permission-aware automation

Treat source permissions as explicit capabilities. Prefer official APIs, authorized integrations, and permitted import workflows. Make automation visible, controllable, and reversible wherever possible.

### 7.8 Open-source and deployment flexibility

A local-first or self-hosted core may appeal to privacy-conscious users and support transparent development. A later hosted tier could offer convenience or resource-intensive features, but would require a separate assessment of privacy, security, costs, and source permissions.

---

## 7A. Revised MVP Recommendation (2026-11-11)

This revises the original plan to aggregate JobsDB, LinkedIn, OfferToday, and CTgoodjobs in the MVP. It is a recommendation for discussion, not a decision.

**Recommended shape**

1. **Hosted workspace as the core:** profile, evidence library, CV versions, per-application workspace, and matching with separate eligibility flags.
2. **Browser extension as the entry point:** one-click capture of the job the user is viewing, and later autofill that stops before submit.
3. **Server-side discovery only from Tier 1 sources** (Section 5.4): Workday-style public endpoints and company career pages first.
4. **Digest and alerts** (email, Telegram, or WhatsApp) once discovery works.

**Scope changes**

- "Aggregate from multiple boards" becomes "capture from any board, auto-discover from permitted sources."
- Application tracking moves from optional into the MVP, because the application record is the backbone of the working tools.
- Application Q&A, interview preparation, and mock interviews remain optional and follow the roadmap phases.
- Human-in-the-loop autofill is kept, with submission always by the user.

**Open decisions**

- **Hosted or local-first.** Hosted plus extension suits non-technical users; local-first avoids data-handling duties. A hybrid (local core, optional hosted features) is possible.
- **First segment.** Non-local students and IANG graduates have the clearest design evidence among the reviewed tools; targeting "everyone" makes eligibility logic harder.
- **Model provider and data flow.** Even local tools send job text and profiles to an LLM provider; decide what is sent and disclose it.

---

## 8. Risks and Non-Goals for the Initial MVP

Avoid these traps:

1. **Building a scraper before validating the product.** Source access and terms can dominate development effort.
2. **Treating a single match percentage as truth.** Matching needs explanations and evidence.
3. **Allowing AI to invent candidate credentials.** Ground outputs in confirmed facts.
4. **Automating final submissions without review.** Important declarations require user oversight and must respect platform rules.
5. **Building every modality immediately.** Video interviews, autonomous application submission, and extensive source coverage can wait.
6. **Confusing local storage with complete privacy.** Model providers, API keys, backups, telemetry, and deletion still matter.
7. **Copying a competitor's protected implementation.** Learn from product patterns; independently design and implement. Check the actual license before reusing open-source code.
8. **Assuming "the user's own browser" is automatically compliant.** JobsFlow and HK Job Radar use it, but their documentation does not establish that it satisfies each platform's terms.
9. **Competing on catalogue size.** hkjob.app lists about 1,060 jobs; a thin aggregate will not beat the major boards.
10. **Relying on the LLM alone for eligibility.** Visa, language, and district signals need structured extraction and deterministic checks.
11. **Overstating privacy.** HK Job Radar says data stays on the machine while sending profile and job text to a model API. Any privacy claim here must account for the model provider.

---

## 9. Recommended Next Steps

1. Inspect the current code, architecture, and license of LuJie CareerKit, JobsFlow, and HK Job Radar before deciding whether any components are useful references. For JobsFlow, read the unread `docs/` files (scoring rules, prompts, JobsDB handbook); for HK Job Radar, verify the `src/hk_job_radar/` structure and `requirements.txt`.
2. Build a source-acquisition matrix for JobsDB, LinkedIn, Indeed, OfferToday, CTgoodjobs, university portals, Workday-hosted career sites, and public ATS interfaces, using the tiers in Section 5.4.
3. Decide the first user segment and whether the MVP is hosted, local-first, or hybrid (Section 7A).
4. Investigate hkjob.app further: find its pricing, operator, and job sources, and test the CV upload flow, since the pages read so far were thin.
5. Define the core data model: Candidate, ExperienceEvidence, ResumeVersion, Job, JobSource, Application, ApplicationArtifact, InterviewSession, Feedback, and AgentTask. Add structured eligibility fields (work rights, languages by level, district, work mode, disclosed salary) and a separate job-poster versus hiring-company distinction.
6. Prototype the core loop with a manually imported JD and one real candidate profile, including two-stage matching and bounded, evidence-linked CV edits.
7. Test whether evidence-grounded matching and application versioning materially improve the user's own workflow.
8. Add browser-extension functionality only after deciding which actions are technically useful and permitted for each source. Move it earlier if the hosted-plus-extension shape is chosen.
9. Keep commercial plans open until the product has demonstrated repeat use and the source-access strategy has been reviewed.

---

## 10. References

- JobCopilot official website: https://jobcopilot.com/
- HoiSum official website: https://hoisum.hk/
- LuJie CareerKit GitHub repository: https://github.com/Chozzc/Lujie-Careerkit
- hkjob.app: https://hkjob.app/ (pages read: home, `/jobs`, `/zh-Hant/guides`, 2026-11-11)
- JobsFlow GitHub repository: https://github.com/mixxmax/jobsflow (README, LICENSE, SETUP.md, commit history, 2026-11-11)
- HK Job Radar GitHub repository: https://github.com/prui0474/hk-job-radar (rendered README, LICENSE, commit history, 2026-11-11)

**Verification reminder:** Recheck current feature descriptions, pricing, license, platform terms, API policies, and integration requirements before making implementation or commercial decisions.
