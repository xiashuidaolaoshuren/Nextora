# UI Brief

**Status:** Living document. Created 2026-11-20 for the workspace core; later sub-projects add their own sections.
**Audience:** The author, and design tools (for example OpenDesign) that produce a prototype from it.
**Reference:** [LuJie CareerKit UI digest](../research/lujie-careerkit-ui-digest.md) for layout ideas.

## How to use this brief

- **Behavior lives in the specs.** This brief says how things look and where they sit. It links to the spec section for why. If the two disagree, the spec wins and this brief is fixed.
- It never defines data fields or API shapes.
- **Foundations** (Part 1) apply to every sub-project. **Screens** (Part 2) are added per sub-project. When this file passes about 300 lines, split foundations and screens into separate files.
- To brief a design tool, give it Part 1 plus one section of Part 2.

---

# Part 1. Foundations

## 1.1 Tone

Warm, calm and low-density. A job search is stressful, so the interface should feel supportive without hiding information. Wording is plain and kind, and never alarming. Example: *"Unknown. We won't guess. Add your languages to check this."*

Style follows the LuJie reference: warm off-white surfaces, white cards with thin borders and rounded corners, generous spacing, serif page titles with a one-line description under each, small sans-serif body text, one dark primary button per page. Light theme first; a dark theme is later.

## 1.2 Tokens (starting values, adjust in the prototype)

| Token | Use | Starting value |
|---|---|---|
| `surface` | Page background | warm off-white `#FAF8F5` |
| `surface-sidebar` | Sidebar | warm light grey `#F2EFEA` |
| `card` | Cards, panels | white with a 1px `#E6E1DA` border |
| `ink` / `ink-muted` | Text | `#1F1D1A` / `#6B655D` |
| `accent` | Primary buttons, selected nav | deep warm ink `#2B2723` |
| `state-pass` | Pass, confirmed, direct | green `#2F7D5B` on tint `#E6F2EC` |
| `state-conflict` | Fail, conflict | rose `#B4435A` on tint `#F8E7EB` |
| `state-unknown` | Unknown, not specified, unable to assess | neutral grey `#6B655D` on tint `#EEEBE6` |
| `state-stale` | Stale, needs refresh | amber `#A8660F` on tint `#FBEFD9` |
| `state-pending` | Deletion pending, in progress | blue-grey `#476A8C` on tint `#E7EEF5`, striped or animated |
| `state-transfer` | Transferable support | teal `#2C7A7B` on tint `#E3F1F1` |

State colours are semantic. They are never used for decoration, and their tints are stronger than any decorative pastel on the page. Never rely on colour alone: every state also has an icon and a text label.

## 1.3 App shell

- **Left sidebar** in two groups: *Workflow* (Jobs, Applications) and *Your records* (Evidence, CVs, Profile). Jobs is the home page.
- **Sidebar footer** always shows where data lives and the model status: "Stored on this computer · Claude ready", or "Stored on this computer · No model: AI actions are off, everything else works."
- **Page header:** serif title and a one-line description. Primary action at the top right.
- **Page width:** a centred column for form-like pages (Profile, Evidence, CVs). Full width for dense pages (Job page, Applications, Jobs table).
- **The bottom-right corner is kept empty.** It is reserved for the later agent.
- Out of the shell: language switch (English only), recycle bin (Archive and permanent delete cover it), charts and conversion rates.

## 1.4 State semantics

These words and icons mean the same thing on every screen. Spec: core §5, §7.5, §10.

| State | Colour | Icon | Wording rule |
|---|---|---|---|
| Pass | `state-pass` | check | "Meets" plus the basis. Always labelled advisory. |
| Conflict | `state-conflict` | cross | Shown only with a job quote and the profile value that contradicts it. Never for a preference. |
| Unknown | `state-unknown` | question mark | Calm. Says why ("no language level in your profile") and offers the fix link. Never red, never described as a failure. |
| Stale | `state-stale` | circular arrow | Says what changed ("your evidence changed") and offers an explicit refresh. A stale result is never displayed as current. |
| Pending | `state-pending` | spinner or hourglass | Says what is in progress and that it survives a restart where the spec says so. |
| Unable to assess | `state-unknown` | dashed circle | Visibly different from "None". Says why (invalid citation, provider failed) and offers a retry. |
| None (no support) | `state-unknown`, outlined | empty circle | Only when the assessment ran and found no support. |
| Direct / Transferable | `state-pass` / `state-transfer` | filled / half circle | Always with the cited evidence chips and a short note. |

No totals, scores, bands or percentages anywhere (ADR-0004).

## 1.5 Shared patterns

- **Model-action disclosure.** Every button that calls a model states its purpose and sits above a one-line disclosure, for example: *"Sends: job text → Anthropic. Nothing is sent until you click."* One click runs it. In no-model mode the button is disabled and the reason is shown next to it. Spec: core §7.7, §11, ADR-0005.
- **Advisory label.** Eligibility results carry the text "Advisory. Not legal advice or a hiring decision."
- **Quote linking.** Any extracted field or condition that has a source quote links to it. Clicking highlights the quote in the original text.
- **Field origin badge.** *Extracted*, *You edited* or *Manual*, plus *Needs review* where it applies. Corrections are never overwritten by a later extraction.
- **Empty states** are short and friendly, with one clear next action.
- **Destructive actions.** Archive is the default removal. Permanent delete uses a type-to-confirm dialog, and its consequences are stated in the dialog.
- **Sample tag.** Seed data carries a "Sample" tag. A "clear sample data" action and a "reset to first run" action exist (dev panel in a prototype, settings later).
- **Dates:** show absolute dates, with relative hints for deadlines ("in 3 days", "overdue").

---

# Part 2. Screens

## Workspace core

Spec: [workspace core design](../superpowers/specs/2026-11-11-workspace-core-design.md). Desktop-first; mobile only needs to stay readable.

### 2.1 Jobs (home)

- **First run:** a dismissible setup checklist card: read the data notice → fill the profile → upload a CV → confirm evidence → add the first job. Nothing blocks pasting a job first.
- **Needs attention** list at the top (hidden when empty): overdue and upcoming deadlines, evidence suggestions to review, stale Evidence Fit, jobs needing review. Each item links to the right page.
- **Jobs table:** title, hiring company (plus poster when different), eligibility summary as counts ("1 conflict · 2 unknown"), fit freshness (*not run*, *current*, *stale*, never a score), latest Application status, deadline. Search and sort.
- **+ New job:** two modes, *Paste text* and *Enter manually*, plus an optional source link (never fetched). A duplicate paste shows an inline warning, "Looks identical to *Data Analyst · HSBC* from 12 Nov. Open it, or save anyway."

### 2.2 Job page

- **Layout:** the original text pinned on the left; tabs on the right: *Fields* | *Match* | *Applications*. Spec: core §7.4, §7.5.
- **Original pane:** clicking a quote elsewhere highlights it here. Text that looks like instructions to a model is marked "excluded from model input".
- **Fields tab:** each field shows value, origin badge and quote link, edited in place. The **Extract fields with Claude** button has its disclosure line. If extraction fails, a banner says the job is still usable by manual entry.
- **Match tab:** three visually separate sections.
  - *Eligibility (advisory):* a checklist of pass, conflict and unknown, each with the job quote, the profile value, and a fix link for unknown.
  - *Preferences:* lighter informational chips (location, work mode, salary, employment type, level). Never styled as pass or fail.
  - *Evidence fit:* grouped rows, **Must have** then **Nice to have**. Each row has a support label, cited evidence as clickable chips, and a short note. A stale banner covers the whole section, with a **Refresh** button and its disclosure line.
- **Applications tab:** the attempts for this job, and **+ New attempt**. Applications can also exist without any job-page visit, from the Applications screen.

### 2.3 Applications

- **List only**, no board and no progress stepper, so statuses never look like a fixed sequence. Spec: core §7.6.
- Columns: job, status, submitted CV (pinned version or "unknown"), deadline, last change. Search, status filter, sort by deadline. Overdue and upcoming deadlines are highlighted.
- **Side sheet** on row click: a status picker that can jump to any status, notes, application deadline, the CV version, and an append-only history timeline (actual previous and new status, time, note). Corrections appear as visible events.
- **Stale view notice:** "This changed to Interviewing elsewhere. Review it, then try again." No change is made.
- **Retry acknowledgement:** "Already done. Current status: Interviewing." No new event.

### 2.4 Evidence

- Filters: *To review (n)*, *Confirmed*, *Rejected*, grouped by kind (experience, project, education, skill, certification).
- Suggestion card: **Confirm**, **Edit and confirm**, **Reject**, and where it came from ("From CV: Main v2").
- **+ Add evidence** for manual entries. Only confirmed evidence is described as used in matching.

### 2.5 CVs

- Upload area and a version list: label (editable), default star, state badge *Active*, *Archived*, *Deleting…* or *Deleted*.
- Actions: **Suggest evidence with Claude** (disclosure line), set default, archive, permanent delete.
- Permanent delete: type-to-confirm dialog. A *Deleting…* row is inaccessible, with a note that cleanup will resume after a restart. A *Deleted* row shows only the minimal marker, and a note that confirmed evidence was kept.
- A scanned PDF with little or no text shows: "Little or no text found. It may be a scanned PDF. Add evidence manually."

### 2.6 Profile

- Work permission (jurisdiction, restrictions, sponsorship need) in one box. Visa status in a separate box that says it does not determine permission.
- Languages and levels, career stage, preferences (districts, work modes, salary floor, target roles).
- Every fact can be set to **Not specified**, which is a normal choice and is explained as producing "unknown", not a negative.

### 2.7 State matrix

Each state below must be visible in the prototype, either reachable or shown as a labelled variant.

| Screen | States to show |
|---|---|
| App shell | Model ready; no model |
| Jobs | First run (empty, checklist); populated; needs-attention empty and non-empty; duplicate-paste warning; needs-review job |
| Job: Fields | Extracted; user-edited; manual; needs review; extraction running; extraction failed |
| Job: Match, eligibility | Pass; conflict; unknown (missing profile fact); unknown (unrecognized condition); unknown (unverified quote) |
| Job: Match, fit | Not run; current; stale; refresh running; unable to assess (invalid citation); unable to assess (provider failed); none; direct; transferable |
| Applications | Empty; populated; overdue and upcoming deadlines; pinned CV; unknown CV; skipped-status history; correction event; stale-view notice; retry acknowledgement |
| Evidence | To-review list; empty; confirmed; rejected; edit and confirm |
| CVs | Active; archived; default; deletion pending; deleted marker; no extractable text; suggestion running; suggestion discarded after deletion |
| Profile | Empty; partly filled; fully filled; not specified |

### 2.8 Seed scenarios

For realistic prototype content (Hong Kong, written by the author or representative, no copied postings):

- 4 jobs: one English, one Traditional Chinese, one mixed, one needing review. One has a poster that differs from the hiring company.
- 2 CV versions, one with a pending-deletion variant shown separately.
- Evidence in every state, including a suggestion from a CV and a manual item.
- Applications covering saved, applied, interviewing and rejected, with one that skipped a status and one correction event.
- One job with stale Evidence Fit; one with an unable-to-assess row; one with a conflict, with a quote.

### 2.9 Out of scope for this section

Export and delete-all screens, real eligibility rules, real file parsing, mobile polish beyond readability, a dark theme.
