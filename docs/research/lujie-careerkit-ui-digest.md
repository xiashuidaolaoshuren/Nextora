# LuJie CareerKit (录阶): UI and Layout Digest

**Date:** 2026-11-20
**Purpose:** Layout reference for the workspace core prototype. Layout and interaction ideas only.
**Source:** Live demo at https://lujie.chozzc.dev (dashboard, JD match, job library, job archive and application tracking pages, viewed in a browser with sample data), the GitHub README and repository tree ([Chozzc/Lujie-Careerkit](https://github.com/Chozzc/Lujie-Careerkit)).
**Not viewed:** Resume editor, interview assistant, campus database, settings, and the Agent panel. The README's GIFs were not inspected.
**License:** Not checked. Do not copy code, images, or the mascot; borrow layout ideas only.

## 1. Product shape

- Open-source, bilingual (Chinese and English) web app aimed at campus-recruitment and internship seekers.
- Stack from the README: Next.js, React, Prisma and SQLite; it also has a `components.json` (shadcn/ui-style config) and a `src-tauri/` folder, which suggests a desktop wrapper. Runs locally, in Docker, or as an online demo that keeps data and AI keys in the browser.
- Wide scope: resume editor with templates, JD-based resume tailoring, interview assistant with mock interviews, job library, application tracking, campus job database, and a floating Agent that writes only after confirmation.
- AI is optional: editing, library and tracking work without a key.

## 2. App frame

- **Left sidebar** (warm light grey), grouped as *Job-search workflow* (Control Center, Campus Info, Resume Editor, JD Match, Interview Assistant, Job Library, Application Tracking) and *System* (Settings, Recycle Bin).
- **Sidebar footer:** "Online version. Data and AI key are stored in this browser."
- **Top-right icons:** language switch, help (restarts the guided tour), follow-up reminders, GitHub, avatar.
- **Floating Agent button** (a mascot) fixed at the bottom-right of every page.
- **Page header:** serif title, one-line description underneath.
- **Content width:** a centred, fairly narrow column on most pages; the tracking page uses the full width.

## 3. Screens

### Control Center (home, `/dashboard`)
- Four number tiles: applied jobs, active processes, follow-ups due, offers.
- "Recent to-dos": a numbered list of the next actions with dates (follow-up, prepare for test or interview), each linking into the relevant page.
- "Quick start": four links (edit resume, JD match, add application, mock interview).
- "Job-search stages": one horizontal bar per stage with a count.
- First visit starts an 8-step (later 9-step) guided tour with a "skip" button.

### JD Match (`/match`)
- A 3-step wizard: (1) choose a CV and a JD, (2) AI tailoring, (3) preview and edit.
- Step 1 is two panes: a CV picker on the left (library or upload; originals and optimized versions are listed together), and on the right a JD paste box with an 8,000-character counter, a voice-input button, an upload-JD button, and four option toggles (emphasize project results, add quantified wording without inventing numbers, ATS-friendly, highlight matching skills).
- The result is a tailored copy of the CV, with the original kept. No match score, band, or chart was visible.

### Job Library (`/library`)
- Toggle: by job or by category. Search box and a sort dropdown.
- Jobs are grouped by company; each job is a folder-style card with a "Sample" tag, a stage chip, location, counts of materials (original JD, tailored CV, prep, mock interview) and a last-updated date. Edit-name and move-to-trash actions sit on the card.
- **Job archive page** (`/library/jobs/{id}`): its own table-of-contents sidebar, a 3-card "preparation path" (job analysis, CV output, interview material), a list of generated materials, and a donut chart of the material mix.

### Application Tracking (`/pipeline`)
- Four tiles (tracked jobs, active processes, interview conversion %, offer conversion %), a stage bar chart and a source (channel) donut.
- Search, a Board / List toggle, and five filters (company, stage, channel, applied date, next follow-up).
- Board columns follow the stages: applied, test or assessment, interviewing, offer, rejected, archived.
- Each card shows channel, stage date, next follow-up date and the bound CV version, plus a progress stepper across the main stages.
- Stages are campus-recruitment oriented (assessment, interview round).

## 4. Visual style (observed)

- Warm off-white and light-grey surfaces, white cards with thin borders and rounded corners, generous white space, low density.
- Serif page titles, small sans-serif body text.
- Muted pastel chart colours (dusty blue, sage, lilac, rose, sand) used for stages; a few saturated bars (black, orange, blue, green, red, grey) appear on the dashboard stage list.
- One dark primary button per page; toggles are dark when on.

## 5. What this means for the workspace core

**Borrow (layout and interaction ideas)**
1. The warm, low-density look: sidebar grouping, serif page titles with a description line, thin-bordered cards.
2. A sidebar footer that states where data lives and the model status.
3. A "needs attention" list built from dates and review states, linking into the right page.
4. Filters bar plus search on the tracker; centred column for form-like pages, full width for dense pages.
5. A "Sample" tag on seed data, and clear-sample-data and reset actions.
6. A per-job archive page with its own table of contents, as the later home for materials and interview preparation.

**Deliberately different**
1. **Matching:** LuJie matches a CV against a JD and rewrites the CV. The core assesses confirmed Evidence against a Job (Evidence Fit, ADR-0002 and ADR-0004) and the CV is not the unit. A tailoring wizard belongs to the later materials sub-project.
2. **No scores or conversion rates:** we show no percentages or charts of outcomes; the core makes no aggregate fit claim.
3. **No progress stepper or fixed board columns:** a candidate may jump to any status without inventing skipped history (spec section 7.6). The prototype uses a list and a visible history timeline.
4. **Eligibility is a separate, advisory view** with quoted sources. LuJie has no equivalent.
5. **Agent button and language switch are out of scope** for the core; leave the bottom-right corner free for the later agent.

## 6. Limits of this digest

The live pages are client-rendered and I read them from a browser session on the sample data, so nothing is known about empty states, error states, dark mode, or mobile layout. Anything not listed above was not seen.
