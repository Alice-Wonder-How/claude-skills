# LinkedIn Browser Automation — Technical Playbook

This document captures the exact patterns that work for posting comments on LinkedIn via Claude in Chrome. LinkedIn's frontend uses Quill.js rich text editors, which behave differently from standard form inputs. These patterns were discovered through real posting sessions and are the reliable way to interact with LinkedIn's comment system.

## Why Regular Click+Type Fails

LinkedIn comment boxes are `contenteditable` DIVs powered by Quill.js — not standard `<input>` or `<textarea>` elements. This means:

- The `form_input` tool will fail with "Element type DIV is not a supported form input"
- Clicking the "Add a comment..." placeholder often doesn't activate the text editor
- Even when clicking works, the Quill editor may not register typed text properly
- Multiple comment boxes exist on the page simultaneously (one per visible post)

Don't waste time clicking repeatedly. Go straight to JavaScript injection.

## The Reliable Method: JavaScript DOM Injection

### Step 1: Open the Comment Box

Click the "Comment" button on the target post. This creates the Quill editor instance in the DOM.

```javascript
// The Comment button is in the post's action bar
// Click it using coordinate-based clicking after scrolling the post into view
```

### Step 2: Find the Right Editor

LinkedIn renders multiple `.ql-editor` elements on the page (one per post with an open comment box). You need to target the one that's currently visible in the viewport.

```javascript
const editors = document.querySelectorAll('.ql-editor[data-placeholder="Add a comment\u2026"]');
let targetEditor = null;
for (let i = 0; i < editors.length; i++) {
    const rect = editors[i].getBoundingClientRect();
    if (rect.top > 0 && rect.top < window.innerHeight) {
        targetEditor = editors[i];
        break;
    }
}
```

The viewport visibility check (`getBoundingClientRect`) is the key insight. Don't try to guess by index — the DOM order doesn't match visual order after scrolling.

### Step 3: Inject the Text

```javascript
if (targetEditor) {
    targetEditor.focus();
    targetEditor.innerHTML = '<p>your comment text here</p>';
    targetEditor.dispatchEvent(new Event('input', { bubbles: true }));
}
```

Three things happen here:
1. **focus()** — activates the editor so LinkedIn's UI recognizes it
2. **innerHTML** — sets the content directly, bypassing Quill's input handling
3. **dispatchEvent('input')** — tells Quill and LinkedIn's React layer that content changed, which enables the "Comment" submit button

All three are required. Skip any one and the post won't work.

### Step 4: Submit the Comment

After injection, a blue "Comment" button appears below the text box. Click it with the `computer` tool using coordinates.

```javascript
// Find and click the submit button
// It's typically at the bottom-right of the comment box area
```

### Step 5: Verify

Check that the comment count on the post incremented by 1. Take a screenshot to confirm your comment appears with "Alice Shikova · You" and the correct text.

## Escaping Special Characters

When injecting text via innerHTML, escape these characters:
- Single quotes: `\'` (in the JavaScript string)
- HTML entities: `&` → `&amp;`, `<` → `&lt;`, `>` → `&gt;`
- Dollar signs in template literals: `\$`

For the `innerHTML` approach, wrap text in `<p>` tags. Multi-paragraph replies use multiple `<p>` tags.

## Extracting Post Links

LinkedIn posts use activity URNs as identifiers. The link format is:
```
https://www.linkedin.com/feed/update/urn:li:activity:{activityId}/
```

To extract URNs from the current DOM:
```javascript
const posts = document.querySelectorAll('[data-urn]');
const results = [];
for (const post of posts) {
    const urn = post.getAttribute('data-urn');
    if (urn && urn.includes('activity')) {
        const activityId = urn.split(':').pop();
        results.push({
            activityId,
            link: `https://www.linkedin.com/feed/update/urn:li:activity:${activityId}/`
        });
    }
}
```

To match URNs to specific authors, search the post's text content:
```javascript
for (const post of posts) {
    const text = post.textContent;
    if (text.includes('AuthorName') || text.includes('keyword from post')) {
        // matched
    }
}
```

Note: LinkedIn virtualizes its feed — posts that have scrolled far out of view get removed from the DOM. If you need to find a post that's gone, scroll back to it.

## Common Pitfalls

1. **Clicking the reactions count** instead of the Comment button opens a Reactions modal. If this happens, close it (click X) and try clicking the Comment text/icon more precisely.

2. **`scroll_to` ref jumping to wrong post** — the `find` tool returns refs in DOM order, which may not match visual order. Use `scroll_to` carefully, and verify with a screenshot that you're on the right post.

3. **Text appearing as "-" or garbage** — this happens if you accidentally type into the editor before clearing it. Use `Cmd+A` then `Backspace` to clear, or just use the innerHTML approach which overwrites everything.

4. **Comment button not appearing after injection** — the `input` event wasn't dispatched properly. Re-run the dispatchEvent line.

5. **Multiple comment boxes open** — if you've clicked "Comment" on multiple posts, multiple editors exist. The viewport visibility check handles this, but be aware of it.

## Rate Limiting

LinkedIn may throttle rapid commenting. Signs:
- "You're posting too fast" warning
- Comments silently failing (count doesn't increment)
- Temporary "try again later" messages

If throttled, wait 30-60 seconds between posts. For 20 posts, consider spacing them out over the session rather than rapid-firing all at once.
