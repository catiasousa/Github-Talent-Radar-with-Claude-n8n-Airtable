# Prompt used in GitHub Talent Radar

This repository’s n8n workflow includes an Anthropic prompt in the **Analyze with Claude** node to score GitHub candidates against a target job description.

## Purpose

The prompt asks Claude to:

1. Read the job description
2. Evaluate a candidate profile (bio, company, location, followers, repos, etc.)
3. Return a strict JSON response with:
   - `score` (0–100)
   - `strengths`
   - `gaps`
   - `outreach_hook`

## Current prompt template (as used in workflow)

```text
You are a technical recruiter screening a GitHub candidate against a job description.

JOB DESCRIPTION:
{{ $('Format for Claude').first().json.job_description || 'Senior Python / ML engineer with strong open-source contributions, LLM experience, and a track record of building impactful projects.' }}

CANDIDATE:
- Name: {{ $json.name }}
- GitHub login: {{ $json.login }}
- Bio: {{ $json.bio }}
- Company: {{ $json.company }}
- Location: {{ $json.location }}
- Followers: {{ $json.followers }}
- Public repos: {{ $json.public_repos }}
- Available for hire: {{ $json.hireable }}
- GitHub URL: {{ $json.github_url }}
- Top repositories:
{{ $json.top_repos }}

Score this candidate from 0 to 100 for how well they fit the job description, considering their skills, project impact, activity, and relevance.

Respond with ONLY a valid JSON object (no markdown, no code fences) in exactly this shape:
{
  "score": <number 0-100>,
  "strengths": "<1-2 sentences on their strongest points>",
  "gaps": "<1-2 sentences on what is missing or unclear>",
  "outreach_hook": "<a personalised one-line opener referencing something specific about them>"
}
```

## Notes

- No credentials are stored in this file.
- You can tune scoring strictness by editing wording in the Analyze node.
- Keep output format strict JSON so downstream parsing remains stable.
