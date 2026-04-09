# Claude Code Skills

A collection of custom Claude Code skills for marketing automation, social engagement, and job hunting.

## Skills

| Skill | Description |
|-------|-------------|
| **reddit-trend-hunter** | Finds relevant Reddit posts for Arrays.org engagement using Apify scraping + Notion tracking |
| **linkedin-engagement** | LinkedIn thought leader engagement via Claude in Chrome browser automation |
| **arrays-image-style** | Generate images and visuals in the Arrays.org brand style using Python + Pillow |
| **job-hunter** | Personal job hunting agent — finds openings, drafts cover letters, selects CVs |
| **weekly-reddit-scouting** | Scheduled task for weekly Reddit scouting for Arrays.org |

## Structure

Each skill folder contains:
- `SKILL.md` — The main skill definition (frontmatter + instructions)
- `references/` — Supporting files (voice guides, playbooks, templates)

## Setup

1. Copy a skill folder to `~/.claude/skills/`
2. Restart Claude Code
3. The skill will be available as a slash command

## Note

Sensitive data (API tokens, emails, Notion IDs) has been redacted. Replace `YOUR_*` placeholders with your own values before use.
