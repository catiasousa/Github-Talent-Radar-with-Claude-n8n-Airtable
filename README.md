# GitHub Talent Radar, n8n + Claude + Airtable

## What this project does, read this first

This project builds an automated sourcing system that runs nightly.
It searches GitHub using multiple discovery methods, analyzes each profile with Claude, scores candidate fit for your target role, and saves high-signal candidates into Airtable, including a personalized outreach line.

After setup, the process is hands-off.
Each morning, Airtable is pre-filled with fresh, ranked candidates ready for outreach.

## Why this exists

Manual GitHub sourcing is slow, repetitive, and difficult to scale consistently.
This workflow turns sourcing into a repeatable system with consistent search patterns, structured AI evaluation, and clean candidate tracking.

## The 5 Search Engines This Model Runs Every Night

The current model runs **5 searches**.

GitHub lets you search for engineers in **5 completely different ways**.  
Each one finds a different kind of person.

Your automation runs all 5 simultaneously every night and combines the results into one ranked talent pipeline.

### Search 1 — By Location  
**Find engineers in a city or country**

**What it does:** Finds engineers who have set a specific city or country in their GitHub profile. Sorted by number of followers, so the most respected engineers appear first.

**Why it matters:** This is how you find engineers who are physically based where you need them, or who describe themselves as remote.

---

### Search 2 — By Programming Language  
**Find specialists in a specific language**

**What it does:** Finds the most-followed engineers whose primary coding language matches what you need. No location filter, global search.

**Why it matters:** For roles where language matters more than location (for example, a CUDA/C++ expert), this finds the best people in the world regardless of where they are.

---

### Search 3 — By Repository Contributor  
**Find people who built the exact thing you need**

**What it does:** Gets the list of everyone who has contributed code to a specific GitHub repository. These are not people who just starred it, they actually wrote code that was accepted.

**Why it matters:** This is the highest-signal search. If you're hiring an LLM inference engineer and you pull contributors from the vLLM repo, every person on that list has proven hands-on expertise in exactly what you need.

---

### Search 4 — By Library or Topic  
**Find engineers working in a specific domain**

**What it does:** Finds repositories tagged with a specific topic (like `llm` or `vector-database`), then pulls contributors from those repos. This finds engineers working in a domain, not just a single project.

**Why it matters:** Someone might not contribute to the main vLLM repo but has built their own LLM inference project with 300 stars. This search finds them.

---

### Search 5 — By Company Organisation  
**Mine specific companies’ engineering teams**

**What it does:** Gets every engineer who is a public member of a company’s GitHub organisation. These are people who work or worked at that company and have made their membership public.

**Why it matters:** This is competitor and peer company mining. 

## 4 accounts you need to create

1. **GitHub**, github.com, your source of candidates  
2. **Anthropic**, console.anthropic.com, Claude AI scoring  
3. **n8n**, n8n.io, the automation engine that connects everything  
4. **Airtable**, airtable.com, your candidate database

## 1-minute quickstart

1. Open n8n and create a new workflow.
2. Open n8n AI Assistant.
3. Paste the full prompt from `PROMPT.md`.
4. Connect GitHub, Anthropic, and Airtable credentials.
5. Run a manual test and verify rows in Airtable.
6. Activate the workflow.

## How the workflow operates

1. Scheduled trigger starts the workflow.
2. GitHub discovery searches for candidate profiles.
3. Candidate data is normalized and filtered.
4. Claude evaluates role fit and writes summary notes.
5. Qualified candidates are saved to Airtable.
6. Recruiter reviews candidates and sends outreach.

## Repository structure

```text
workflow/
  github-talent-radar.json
README.md
PROMPT.md
```

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

## Setup details

### 1) Import workflow
In n8n, import `workflow/github-talent-radar.json`.

### 2) Connect credentials
Add credentials for GitHub, Anthropic, and Airtable in n8n.

### 3) Configure sourcing criteria
Define target roles, skill keywords, geography preferences, and activity thresholds.

### 4) Run test
Execute one manual run, confirm records are written to Airtable, review summary quality.

### 5) Activate automation
Turn on the workflow so it runs on schedule.

### After importing the workflow

The `workflow/github-talent-radar.json` file ships with placeholders, you must replace these before the workflow will run.

**1. Airtable base & table**  

Open the **Save to Airtable** node and set your own destination:
- `YOUR_AIRTABLE_BASE_ID` → your Airtable base
- `YOUR_AIRTABLE_TABLE_ID` → your Airtable table

Or select them from the dropdowns once your Airtable credential is connected.

Make sure your table includes these fields:
`full_name`, `github_url`, `location_city`, `current_company`, `source`, `date_sourced`, `fit_score`, `key_strengths`, `key_gaps`, `outreach_hook`, `profile_summary`.

**2. Credentials**  

This template ships with **no credentials attached**. On import, connect your own:
- **GitHub** (`githubApi`), used by all GitHub HTTP Request nodes
- **Anthropic** (`anthropicApi`), used by **Analyze with Claude**
- **Airtable** (`airtableTokenApi`), used by **Save to Airtable**

**3. Customize your search (optional)**  

The 5 search nodes use example seeds (Python developers, `vllm-project/vllm`, `llm`, `huggingface`).  
Edit Search 1–5 URLs to match your hiring targets.

**4. Schedule**  

Runs daily at **02:00** in your n8n instance timezone.  
Change this in **Schedule Trigger**.

## Operating notes

- Keep API keys in secure credential managers only.
- Tune prompts and filters weekly to improve candidate quality.
- Add deduplication logic to avoid repeated profiles.
- Use status fields in Airtable to track outreach progress.

## Roadmap

- Add weighted scoring by role requirements.
- Add profile deduplication by username and URL.
- Add Slack or email alerts for high-match candidates.
- Add monthly sourcing analytics dashboard.

## License

MIT, recommended for portfolio projects.
