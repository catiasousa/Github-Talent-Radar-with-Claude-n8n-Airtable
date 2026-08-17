# GitHub Talent Radar, n8n + Claude + Airtable

![Workflow](n8n%20Gihub%20workflow.png)

## What this project does

This project builds an automated sourcing system that runs nightly. It searches GitHub using multiple discovery methods, analyzes each profile with Claude, scores candidate fit for your target role, and saves high-signal candidates into Airtable, including a personalized outreach line.

After setup, the process is hands-off. Each morning, Airtable is pre-filled with fresh, ranked candidates ready for outreach.

## Why GitHub

Manual GitHub sourcing is slow, repetitive, and difficult to scale consistently. This workflow turns sourcing into a repeatable system with consistent search patterns, structured AI evaluation, and clean candidate tracking.

## Workflow logic explained

This workflow runs weekly and applies a consistent sourcing pipeline to GitHub profiles.

It begins with multiple discovery paths to capture different engineering signals, then merges and de-duplicates profiles so each candidate is evaluated once.

Each profile is enriched with practical context from GitHub activity, then sent to Claude for structured scoring including fit score, strengths, gaps, and priority action.

Candidates meeting your threshold are saved to Airtable with standardized fields and outreach hooks, so your review and contact process stays fast and repeatable.

The result is a reliable shortlist generation loop based on real builder activity, not just keyword matching.

## Who this is for (and not for)

### This is for you if
- You recruit software, platform, or AI engineering talent and want sourcing signals from real code activity.
- You want to identify candidates through practical indicators such as repositories, contribution patterns, and project ownership.
- You want a weekly workflow that scores profiles, prioritizes outreach, and writes structured candidate records to Airtable.

### This is not for you if
- You only hire roles that are not evaluated through technical output.
- You cannot use external APIs or automated profile analysis due to policy constraints.

## The search engines this workflow runs

This workflow runs **5 searches** in parallel and merges results into one ranked candidate pipeline.

### Search 1 — By Location
Find engineers in a city or country.

What it does: Finds engineers who set a specific location in their GitHub profile, sorted by followers.

Why it matters: Helps target geography-specific hiring needs.

### Search 2 — By Programming Language
Find specialists in a specific language.

What it does: Finds highly followed engineers associated with the language you need.

Why it matters: Useful when language depth matters more than location.

### Search 3 — By Repository Contributor
Find people who built the exact thing you need.

What it does: Pulls contributors from a target repository.

Why it matters: Very high-signal proof of hands-on experience in the domain.

### Search 4 — By Library or Topic
Find engineers working in a specific domain.

What it does: Finds topic-tagged repositories and pulls contributor profiles.

Why it matters: Surfaces domain practitioners beyond one flagship repo.

### Search 5 — By Company Organisation
Mine specific companies’ engineering teams.

What it does: Pulls public members from a company GitHub organisation.

Why it matters: Supports competitor and peer-company sourcing.

## Accounts you need to create

1. **GitHub** (github.com), your source of candidates.
2. **Anthropic** (console.anthropic.com), Claude AI scoring.
3. **n8n** (n8n.io), the automation engine that connects everything.
4. **Airtable** (airtable.com), your candidate database.

## How to get each API key

- **GitHub**: Settings → Developer settings → Personal access tokens → create a fine-grained token with public read access for user/profile lookups.
- **Anthropic**: console.anthropic.com → API Keys → Create Key.
- **Airtable**: airtable.com/create/tokens → create token with `data.records:write` and `schema.bases:read` access for your base.
- **n8n**: no API key required for n8n itself; create credentials inside n8n for GitHub, Anthropic, and Airtable.

> Never paste secrets into workflow JSON or repository files. Store them only in n8n credentials.

## Quickstart, two ways to build it

### Option A — Import the ready-made workflow
1. In n8n, click **Add workflow → Import from File** (or **Import from URL**).
2. Import `workflow/github-talent-radar.json` from this repo.
3. Connect your credentials and set your Airtable destination (see Setup details below).
4. Run a manual test, verify rows in Airtable, then activate.

### Option B — Rebuild it from a single prompt
1. Open n8n and create a new workflow.
2. Open the **n8n AI Assistant**.
3. Paste the full prompt from [`PROMPT.md`](PROMPT.md).
4. Connect credentials, test, and activate.

`PROMPT.md` describes every node so Claude / the n8n AI Assistant can reconstruct the workflow from scratch, handy if you want to understand or customize each step.

## How the workflow operates

1. Scheduled trigger starts the workflow.
2. GitHub discovery searches for candidate profiles.
3. Candidate data is normalized and filtered.
4. Claude evaluates role fit and writes summary notes.
5. Qualified candidates are saved to Airtable.
6. Recruiter reviews candidates and sends outreach.

## Airtable fields required by this workflow

- full_name
- github_url
- location_city
- current_company
- source
- date_sourced
- fit_score
- key_strengths
- key_gaps
- outreach_hook
- profile_summary

Optional **manual tracking** columns (not filled by the workflow, add them if you want to track outreach in Airtable):

- current_company
- contacted
- replied

## Setup details

### 1) Import workflow
In n8n, import `workflow/github-talent-radar.json`.

### 2) Connect credentials
This template ships with **no credentials attached**. On import, connect your own:
- **GitHub** (`githubApi`), used by GitHub request nodes.
- **Anthropic** (`anthropicApi`), used by **Analyze with Claude**.
- **Airtable** (`airtableTokenApi`), used by **Save to Airtable**.

### 3) Point at your Airtable base
Open the **Save to Airtable** node and set:
- `YOUR_AIRTABLE_BASE_ID`
- `YOUR_AIRTABLE_TABLE_ID`

Or select them from dropdowns after connecting Airtable credentials.

### 4) Configure sourcing criteria
Define target roles, skill keywords, geography preferences, and activity thresholds.

### 5) Run test
Execute one manual run, confirm records are written to Airtable, and review summary quality.

### 6) Activate automation
Turn on the workflow so it runs on schedule.

### 7) Schedule
Runs daily at **02:00** in your n8n instance timezone. Change this in **Schedule Trigger**.

## Customizing

- Change discovery seeds and filters in Search 1–5 to match your hiring goals.
- Tune Claude prompt strictness for different seniority or role profiles.
- Add deduplication or exclusion rules for already-contacted candidates.
- Adjust Airtable field mappings if your schema differs.

## Repository structure

```text
workflow/
  github-talent-radar.json
.github/
  workflows/
    gitleaks.yml
README.md
PROMPT.md
.gitleaks.toml
.gitignore
LICENSE
```

## Security notes

This repo is a public workflow template. It should contain logic only, never live secrets or personal data.

What is safe to publish: workflow structure, node logic, public URLs, field mappings, and credential type references (for example `githubApi`, `anthropicApi`, `airtableTokenApi`).

What must not be committed: API keys, tokens, n8n credential IDs, webhook secrets, `.env` files, private keys, or candidate personal contact data.

Use placeholders like `YOUR_AIRTABLE_BASE_ID`, `YOUR_AIRTABLE_TABLE_ID`, and `EXAMPLE_ORG`.

Gitleaks runs via `.github/workflows/gitleaks.yml`. Run locally before push:

`gitleaks detect --config .gitleaks.toml --source . --no-git -v`

## Operating notes

- Keep API keys in secure credential managers only.
- Tune prompts and filters weekly to improve candidate quality.
- Add deduplication logic to avoid repeated profiles.
- Use status fields in Airtable to track outreach progress.

## License

MIT.
