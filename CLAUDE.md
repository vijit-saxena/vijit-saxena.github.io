# CLAUDE.md

Guidelines for creating blog posts on this site.

## Post template

Use `content/posts/outage-01.md` as the reference template for all outage posts.

### Front matter

```toml
+++
date = '2026-02-27T02:03:12+05:30'
draft = false
title = 'Outage - <Short Descriptive Title>'
+++
```

- `date`: use the actual date in ISO 8601 format with IST offset (`+05:30`)
- `draft`: set to `false` when ready to publish
- `title`: prefix outage posts with `Outage - ` followed by a short, catchy title

### Sections (required, in order)

1. **## Background** — context about the system and what led up to the outage. Cover: what the system does, the relevant component, and the gap or shortcut that made the outage possible.

2. **## Outage** — what went wrong. Open with a numbered list of root causes, then add one `###` subsection per root cause explaining each in detail.

3. **## Resolution** — numbered list of steps taken to recover, in chronological order. Each step should have a short bold title followed by explanation.

4. **## Learnings** — numbered list of takeaways. Each item should be a concrete, actionable lesson derived from the outage.

5. **## Actions** — numbered list of follow-up items the team committed to after the outage. Write in present/ongoing tense (e.g. "Team has started…", "Discussions are ongoing…").

### Writing style

- First-person plural ("we", "our") throughout.
- Conversational but technical — assume the reader is an engineer.
- No fluff. Every sentence should add context, cause, or consequence.
- Spell out the impact clearly (e.g. number of records affected, latency introduced, revenue/credibility cost).

## Running the site locally

```bash
hugo server        # serves at http://localhost:1313
hugo server -D     # includes draft posts
```

## Creating a new post

```bash
hugo new content/posts/<slug>.md
```
