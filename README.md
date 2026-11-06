# EA Practice Tracker

An interactive 50-question Enrolled Agent practice tracker. It rotates across the three Special Enrollment Examination parts, scores each attempt, records section performance, and uses missed-topic patterns to guide future study.

## What this project does

- Delivers a 50-question practice exam.
- Covers Individuals, Businesses, and Representation, Practices, and Procedures.
- Records total score and section performance.
- Maintains a missed-topic study list.
- Uses repeated missed topics to guide the next exam.
- Uses a Google Sheet as the shared score database so laptop, office desktop, and phone can use the same history after sync is live.

## Live Tracker

https://fred-ea-practice-tracker.samoanfro.chatgpt.site

## System Architecture

```text
EA Practice Tracker website
        |
        | score and answer results
        v
Google Apps Script web app
        |
        v
Google Sheet
  - Dashboard
  - Score Log
  - Question Results
  - Study Focus
  - Question Bank
```

## Repository Structure

```text
.
├── README.md
├── docs/
│   └── EA_Practice_Tracker_GitHub_Handoff.docx
├── site/
│   └── tracker website source files
├── apps-script/
│   └── Code.gs
├── question-bank/
│   └── question content and source references
└── .gitignore
```

Create the `site`, `apps-script`, and `question-bank` folders when the current live source files are exported or copied into this repository.

## Required Google Sheet Tabs

| Tab | Purpose |
| --- | --- |
| Dashboard | Manager-style score summary and study trend view |
| Score Log | One row per exam section result |
| Question Results | Answer-level history used to find repeated misses |
| Study Focus | Ranked missed-topic list |
| Question Bank | Question metadata, topics, answers, tax year, and sources |

## Local Development Setup

1. Clone the repository.
2. Keep the live Google Sheet private and shared only with authorized users.
3. Add the website source under `site/`.
4. Add the Apps Script source under `apps-script/Code.gs`.
5. Use [clasp](https://github.com/google/clasp) if you want Apps Script source to deploy from GitHub.
6. Test a complete exam submission before changing the public tracker.

## Secrets and Private Values

Do **not** commit secrets or live configuration values to GitHub. In particular, do not commit:

- `EA_SYNC_KEY`
- Google Sheet IDs, if the repository will be public
- API keys, passwords, session tokens, or OAuth credentials
- Student answer history or personal score exports

Store the Apps Script sync key in Apps Script Properties instead of a source-code constant:

```javascript
const EA_SYNC_KEY = PropertiesService
  .getScriptProperties()
  .getProperty('EA_SYNC_KEY');
```

Set it in Apps Script under **Project Settings → Script properties**. Use placeholders in committed code:

```javascript
const EA_SHEET_ID = 'YOUR_PRIVATE_SHEET_ID';
```

Suggested `.gitignore`:

```gitignore
.env
.env.*
!.env.example
clasp_credentials.json
appsscript.json.local
score-exports/
*.csv
*.xlsx
```

## Current Sync Status

The tracker works on the device where an exam is taken. Cross-device score history and adaptive study focus depend on the Apps Script web app returning valid results from the shared Google Sheet.

The current Apps Script correction must be saved and deployed through the existing Web App deployment:

1. In Apps Script, save the corrected `getLogs_` function.
2. Choose **Deploy → Manage deployments**.
3. Open the existing Web App deployment with the pencil icon.
4. Choose **New version** and select **Deploy**.
5. Do not create a separate deployment; keep the same Web App URL.
6. Refresh the tracker on each device.

## Adaptive Exam Rules

- Preserve coverage across all three SEE parts.
- Do not repeat the same question or near-duplicate fact pattern from recent exams.
- Increase weight for missed topics from prior exams.
- Flag questions tied to annual inflation adjustments, filing thresholds, or phaseouts with the applicable tax year.
- Use current IRS guidance and federal tax law appropriate to the applicable EA examination period.
- Exclude Sundays from the scheduled daily exam cadence.

## Daily User Workflow

1. Open the live tracker.
2. Take the interactive 50-question exam.
3. Submit answers and review overall and section scores.
4. Open **Study Focus**.
5. Study the two highest-priority missed topics for about 20 minutes each.
6. Take the next exam on the next scheduled practice day.

## Recommended GitHub Workflow

Use one repository as the source of truth for code and documentation:

1. Make changes in a branch.
2. Test on a non-production copy when making major changes.
3. Commit with a plain description, such as `Fix shared score sync date handling`.
4. Deploy the Apps Script update as a new version of the existing Web App.
5. Update the site only after the Apps Script endpoint is verified.
6. Keep exam questions and IRS sources versioned so stale tax-law questions can be replaced deliberately.

## Before You Make the Repository Public

- Remove private keys and live IDs from commit history.
- Confirm that the Google Sheet is not public.
- Remove personal score history and answer-level data.
- Review question sources and avoid copying proprietary commercial course content.
- Keep only original questions or material that may be lawfully used.

## Owner Notes

The current priority is finishing the shared score-sync deployment, then verifying that both the laptop and office desktop show the same score history and Study Focus list. Once that is confirmed, adaptive exam selection can use one shared missed-topic pattern across devices.
