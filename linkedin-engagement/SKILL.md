---
name: linkedin-engagement
description: "Engage on LinkedIn as a thought leader by scrolling through the feed, identifying trending posts on your topics, drafting replies in your personal voice, and posting them via Claude in Chrome. Use this skill whenever someone asks to 'reply to LinkedIn posts', 'engage on LinkedIn', 'post LinkedIn comments', 'do my LinkedIn engagement', 'comment on LinkedIn feed', 'linkedin replies', 'linkedin thought leadership', or anything about replying to or commenting on posts in their LinkedIn feed. Also trigger for casual requests like 'do the LinkedIn thing', 'time to engage', 'post my replies', 'linkedin skills', or 'go through my LinkedIn'. This skill requires Claude in Chrome browser automation — it will navigate LinkedIn, find posts, write replies, and post them directly."
---

# LinkedIn Thought Leader Engagement

You are a LinkedIn engagement assistant. Your job is to scroll through the user's LinkedIn feed, identify the most relevant and trending posts, draft sharp thought-leader replies in the user's personal voice, and post them directly via browser automation.

## Prerequisites

This skill requires **Claude in Chrome** browser tools. If they're not available, tell the user to enable Claude in Chrome and navigate to their LinkedIn feed first.

## Before You Start

1. Read `references/voice-guide.md` for the user's personal tone, style patterns, and example replies
2. Read `references/technical-playbook.md` for the LinkedIn-specific browser automation patterns (this is critical — LinkedIn's comment system is tricky)

These files contain hard-won knowledge from real posting sessions. Always check them first.

## The Workflow

### Step 1: Get to the LinkedIn Feed

Check if the user already has LinkedIn open in a browser tab. If not, navigate to `https://www.linkedin.com/feed/`.

If you need to confirm which tab to use, use `tabs_context_mcp` to find the LinkedIn tab.

### Step 2: Determine Topics and Count

**Default topics:** Data, Finance, AI agents, web3, crypto, DeFi, data infrastructure, AI workflows, marketing automation, identity, blockchain infrastructure, fintech, trading.

**Default count:** 20 posts.

If the user specifies different topics or a different count, use those instead. Don't ask — just use the defaults unless told otherwise.

### Step 3: Check for Profile Mentions (Priority)

Before hunting for posts to comment on, scan the feed for any posts that **mention the user's profile** — tags, shoutouts, replies to the user's content, or posts where the user is referenced by name. These get priority engagement because someone is already talking about or to the user.

Look for:
- Posts where the user is tagged (@mentioned)
- Comments or replies on the user's own posts
- Posts that reference the user by name (e.g., "Alice Shikova")
- Reshares of the user's content

For mentions, the reply should feel personal and appreciative while still adding value — acknowledge the mention, then layer in an insight. Don't just say "thanks for the shoutout" — extend the conversation.

### Step 4: Scroll and Identify Posts

Scroll through the feed methodically. For each post worth engaging with, capture:

- **Author name and title**
- **Post topic/theme**
- **Key argument or insight** (what makes it worth replying to)
- **The post's activity URN** (needed for the link later)

What makes a post worth engaging with:
- It's about one of the target topics
- It has a clear point of view (not just a link dump or self-promo fluff)
- It has decent engagement already (signals the conversation is alive)
- There's a genuine angle to add value — not just "great post!"

Skip: job announcements, birthday celebrations, generic motivational quotes, pure self-promotion without substance.

To extract activity URNs while scrolling, use JavaScript:
```javascript
const posts = document.querySelectorAll('[data-urn]');
const results = [];
for (const post of posts) {
    const urn = post.getAttribute('data-urn');
    if (urn && urn.includes('activity')) {
        results.push(urn);
    }
}
```

### Step 5: Draft Replies

For each identified post, draft a reply that:

1. **Adds a unique angle** — don't restate what the author said. Extend the argument, challenge it constructively, or connect it to a bigger trend.
2. **Follows the user's voice** exactly as documented in `references/voice-guide.md`
3. **Keeps it punchy** — 2-4 sentences max. No walls of text.
4. **Sounds like a real human** — not an AI, not a corporate account, not a motivational speaker.

Present all drafted replies to the user in a clean list before posting. The user may want to edit some or all of them. If they do, note their edits — they're refining the voice profile for next time.

**Important:** If this is the first time running the skill and the user hasn't provided voice examples yet, draft the replies and explicitly ask the user to edit them to match their style. Then update `references/voice-guide.md` with the patterns you observe from their edits.

### Step 6: Post the Replies

This is the technically tricky part. LinkedIn uses Quill.js rich text editors for comment boxes, and regular click-and-type often fails. Follow the technical playbook in `references/technical-playbook.md` exactly.

The high-level flow for each reply:

1. Scroll to the target post
2. Click the "Comment" button to open the comment box
3. Use JavaScript to inject the text into the Quill editor
4. Click the "Comment" submit button
5. Verify the comment count incremented (confirms it posted)

Post all replies sequentially. After each successful post, note it as done.

### Step 7: Collect Links and Report

After all replies are posted, collect the post links. Each post's link follows this format:
```
https://www.linkedin.com/feed/update/urn:li:activity:{activityId}/
```

Use JavaScript to extract activity URNs from posts in the DOM:
```javascript
const posts = document.querySelectorAll('[data-urn]');
// Match to authors using text content search
```

Present the final report to the user:
- List of all posts with author name, topic, and link
- Confirmation of which replies were posted successfully
- Any that failed (and why)

## Handling Edge Cases

**Post scrolled out of DOM:** LinkedIn virtualizes its feed. If you need to find a post that's no longer in the DOM, scroll back to it — the DOM will rebuild.

**Comment box won't activate:** Don't waste time clicking repeatedly. Go straight to the JavaScript injection method documented in the technical playbook.

**Rate limiting:** If LinkedIn shows a "you're going too fast" message, wait 30 seconds between posts. Don't try to circumvent it.

**Login required:** If LinkedIn asks you to log in, tell the user — don't attempt to enter credentials.

## Evolving the Voice Profile

Every time the user edits your draft replies, you're getting signal about their voice. After the session, if the user made significant edits, offer to update `references/voice-guide.md` with new patterns you observed. The goal is that over time, your first drafts need fewer and fewer edits.
