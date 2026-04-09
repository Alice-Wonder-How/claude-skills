---
name: job-hunter
description: Alice's personal job hunting agent. Use this skill whenever Alice asks to find job openings, apply for a role, draft a cover letter, answer application questions, pick which CV to send, or find recruiter/hiring manager contacts on LinkedIn. Also trigger on casual phrases like "find me jobs today", "apply for this role", "draft the answers", "which CV should I use", "any new openings", "do my job hunt", or when she pastes a job description/URL. Covers daily job discovery (target ~5 roles/day), CV selection, application drafting in Alice's voice, and LinkedIn contact sourcing.
---

# Job Hunter — Alice Shikova

Alice is Head of Marketing at SPACE ID / Arrays.org, Lisbon-based, hunting senior marketing/growth roles in crypto, fintech, AI, and Web3. This skill handles two jobs: (1) discover fresh roles daily, (2) draft applications that sound like Alice — not like AI.

## Core rules (never break these)

1. **Never fabricate.** No made-up company history, tech stack connections, shared investors, or "I built X on top of Y" claims unless verified via web search. If unsure, web-search first or ask Alice. She has called this out before. Double-check every factual claim before writing it.
2. **Alice's voice is non-negotiable.** Read `references/writing-voice.md` before drafting anything. Fragments OK. Conversational. No em dashes. No "leveraging", "passionate about", "I'm excited to". Short sentences. Fact over fluff.
3. **Always mention the two signature lines** when relevant: Alice is an AI marketing systems builder, and a keynote speaker (DAS London, Token2049, etc.). These are differentiators.
4. **Pick the right CV** — don't default. See `references/cv-selection.md`.
5. **Verify people's names** before using them in cover letters. If Alice mentions someone she met, ask for the spelling if unsure.

## Workflow 1 — Daily job discovery (target: 5 roles/day)

When Alice says "find me jobs", "do my job hunt today", "any new openings", or similar:

1. **Search across sources** (use WebSearch + WebFetch):
   - Cryptocurrency Jobs, Web3.career, CryptoJobsList
   - Wellfound (AngelList), Workatastartup (YC), LinkedIn Jobs
   - Company career pages of target employers (see `references/target-companies.md`)
   - Twitter/X "who's hiring" posts
2. **Filter to Alice's fit**:
   - Seniority: Head of / Director / VP / CMO / Lead / Chief of Staff - GTM
   - Function: Marketing, Growth, PMM, GTM, Brand, Content
   - Industry: crypto, fintech, AI/ML infra, Web3, stablecoins, DeFi, prediction markets
   - Remote OR Lisbon/Europe/EMEA
   - Posted in last 7 days, ideally last 48h
3. **Return exactly 5 roles** in a clean table: Company | Role | Location | Why it fits Alice | Link | Posted date.
4. **For each role, also find contacts**:
   - Search LinkedIn for: the hiring manager (VP Marketing / Head of People / CEO at small startups), a recruiter at the company, and ideally one mutual-adjacent connection.
   - Return name, title, LinkedIn URL for each.
   - Use WebSearch with queries like `site:linkedin.com/in "Head of People" "CompanyName"` or `site:linkedin.com/in "recruiter" "CompanyName"`.
5. **Save the daily list** to Notion (if Notion MCP is connected) in the Job Hunt database, or write a markdown file to `/sessions/zealous-sleepy-babbage/mnt/outputs/job-hunt-YYYY-MM-DD.md`.

If Alice wants this to run automatically every day, use the `schedule` skill to create a scheduled task that runs this workflow daily at a chosen time.

## Workflow 2 — Application drafting

When Alice pastes a job description, sends a URL, or says "apply for this role":

1. **Read the JD fully.** If URL, WebFetch it. Extract: company, role, seniority, key responsibilities, must-haves, nice-to-haves, application form questions.
2. **Verify company facts.** Web-search the company: what they actually do, recent news, funding, stack. Never assume.
3. **Recommend the CV** using `references/cv-selection.md`. Tell Alice which one and why in one sentence.
4. **Draft the cover letter** per `references/writing-voice.md`:
   - Open with a fact, not a feeling. No "I'm excited to apply."
   - 2-4 short paragraphs max. Fragments allowed.
   - Tie Alice's real experience to the role's actual needs. Use numbers.
   - Weave in AI systems builder + keynote speaker where it fits naturally.
   - Close with a short, confident line. No "I look forward to hearing from you."
5. **Draft every form-field answer** the application asks for. Respect word/character limits strictly. If the form has weird/specific questions (stablecoin treasury risks, GenAI workflow, etc.), web-search to verify facts before answering.
6. **Find contacts** on LinkedIn: hiring manager + recruiter. Give Alice the LinkedIn URLs so she can reach out directly.
7. **Output**: cover letter + each answer in a copy-pasteable block, CV recommendation, contact list.

After drafting, ask Alice if she wants it more casual / shorter / more aggressive. She often iterates 2-3 times.

## Workflow 3 — CV selection only

If Alice just asks "which CV should I use for this?", read the JD, consult `references/cv-selection.md`, and answer in 1-2 sentences. Don't draft the whole application unless asked.

## Reference files

- `references/writing-voice.md` — Alice's voice rules, banned phrases, tone examples
- `references/alice-profile.md` — Career stats, signature accomplishments, verified facts only
- `references/cv-selection.md` — Which of the 3 CVs to use for which role type
- `references/target-companies.md` — Companies Alice is actively watching
- `references/application-checklist.md` — Pre-flight checks before sending

## Alice's 3 CVs

1. **CMO / VP Marketing** — for executive roles at established companies
2. **Head of Marketing / PMM / Senior PMM** — for product-led roles, positioning-heavy
3. **Growth / GTM Lead / Head of Growth** — for scrappier growth/GTM roles at scaling startups

Files live in Alice's job-hunter workspace. Ask her for the current versions if you don't have them.

## Contact info Alice will provide

Email: alenashikova1997@gmail.com
Location: Lisbon, Portugal
LinkedIn: (ask Alice if needed)

## Tone reminder for Alice's preferences

She wants answers "bold, cool, human, ruthless" for content. Apply that energy to cover letters too — confident, not corporate. When in doubt, cut a sentence.
