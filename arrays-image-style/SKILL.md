---
name: arrays-image-style
description: Design and generate images, visuals, diagrams, comparison tables, hero images, and data visualizations in the Arrays brand style. Use this skill whenever the user asks to create any image, graphic, chart, table, or visual asset for Arrays — including hero images, comparison tables, feature diagrams, architecture diagrams, social media visuals, or any PNG/image output related to Arrays.org. Also trigger when the user asks to "make it look like Arrays style", "create a visual", "generate an image for the article", or "design something for Arrays". This skill ensures every image output matches Arrays' precise brand identity: black/white/grey palette, Geist Mono + Outfit fonts, editorial whitespace, and technical minimalism.
---

# Arrays Image Style Skill

Use this skill to generate any image asset in the Arrays brand style using Python + Pillow.

Always install Pillow first if not available:
```bash
pip install pillow --break-system-packages -q
```

Fonts are at: `/mnt/skills/examples/canvas-design/canvas-fonts/`

---

## Brand Identity

### Colors
| Role | Value | Usage |
|---|---|---|
| Black | `#000000` | Primary text, key UI elements, hero boxes |
| White | `#FFFFFF` | Background — always white |
| Dark grey | `#333333` | Secondary text, feature labels |
| Mid grey | `#888888` – `#AAAAAA` | Subtitles, metadata, footnotes |
| Light grey | `#CCCCCC` – `#EEEEEE` | Borders, dividers, connector lines |
| Off-white | `#F6F6F6` – `#FAFAFA` | Alternating row fills, card backgrounds |

**Never use color.** Arrays is strictly black, white, and grey. No blues, greens, or accent colors.

### Typography
| Role | Font | Size range |
|---|---|---|
| Hero titles | `Outfit-Bold.ttf` | 44–56px |
| Subtitles / body | `Outfit-Regular.ttf` | 20–24px |
| Column headers | `GeistMono-Bold.ttf` | 20–24px |
| Labels / tags | `GeistMono-Regular.ttf` | 13–18px |
| Footnotes / footer | `GeistMono-Regular.ttf` | 11–13px |
| Technical labels | `GeistMono-Bold.ttf` | 14–18px |

**Always use two fonts together:** Outfit (expressive, editorial) + Geist Mono (technical, precise).

### Logo Usage
The Arrays logo text is: `[[arrays]]`  
**Never include the `ı` pipe characters** on either side — use `[[arrays]]` only.  
The logo appears in `GeistMono-Bold` and is typically placed inside the black hero box or in the footer.

### Layout Principles
- Generous padding: `PAD = 64–72px` on all sides
- Always open with a **2px black horizontal rule** at the top
- Always close with a **1px black horizontal rule** + footer text at the bottom
- Footer text: `"arrays.org  —  Unified Data Engine for Intelligent Finance"` in `GeistMono-Regular` at 12px, color `#BBBBBB`
- Plenty of white space — never crowded

---

## Image Types & Patterns

### 1. Comparison Table
Used for: feature comparisons vs competitors (Bloomberg, CoinGecko, etc.)

**Key rules:**
- Arrays column always has black background header, white cell fill, and 2px black border outline
- Competitor columns have grey `#AAAAAA` headers, no special border
- Alternating row fill: `#F6F6F6` on even rows (skipped in Arrays column — always white)
- Use `YES` / `NO` / text values — never emoji or symbols
- `YES` in Arrays column = `GeistMono-Bold`, `#000000`
- `YES` in competitor columns = `GeistMono-Regular`, `#000000`  
- `NO` anywhere = `GeistMono-Regular`, `#CCCCCC` (greyed out)
- Feature label column = `GeistMono-Regular`, `#333333`
- Light horizontal rules between rows: `#EEEEEE`, 1px

**Canvas size:** 1400 × 740–860px depending on number of rows.

```python
# Column layout reference
col0 = PAD          # feature labels
col1 = 490          # Arrays (highlighted)
col2 = 760          # Competitor 1
col3 = 1080         # Competitor 2
col_w = 250         # column width
row_h = 56          # row height
```

### 2. Hero / Architecture Diagram
Used for: article headers, product explainers, "how it works" visuals.

**Key rules:**
- Title in `Outfit-Bold` top-left, subtitle in `Outfit-Regular` grey beneath it
- Central black box = the Arrays API (black fill, white `GeistMono-Bold` text)
- Source boxes below = light grey fill `#F9F9F9`, grey border `#CCCCCC`, centered `GeistMono-Bold` title + `GeistMono` xs subtitle
- Connector lines from API box center-bottom to each source box top-center: `#DDDDDD`, 1px
- Feature tags at the bottom: small bordered pills, `GeistMono-Regular`, `#555555` text, `#CCCCCC` border
- Draw connector lines BEFORE drawing boxes (so lines appear behind boxes)

**Canvas size:** 1400 × 560–720px

```python
# Standard source box dimensions
box_w, box_h = 138, 60
gap = 20
# Center everything horizontally:
total_w = n * box_w + (n - 1) * gap
start_x = (W - total_w) // 2
```

### 3. General Rules for All Images
- Canvas width: always **1400px**
- DPI: always save at `dpi=(144, 144)` for crisp display
- Save as PNG to `/mnt/user-data/outputs/`
- Never use emoji — use text (`YES`, `NO`, `Partial`, etc.)
- All text must be inside canvas boundaries with proper margins
- Nothing overlaps

---

## Boilerplate Template

```python
from PIL import Image, ImageDraw, ImageFont

FONTS = "/mnt/skills/examples/canvas-design/canvas-fonts"

W, H = 1400, 700  # adjust H per image type
img = Image.new("RGB", (W, H), "#FFFFFF")
draw = ImageDraw.Draw(img)

PAD = 72

# Load fonts
outfit_bold = ImageFont.truetype(f"{FONTS}/Outfit-Bold.ttf", 52)
outfit_reg  = ImageFont.truetype(f"{FONTS}/Outfit-Regular.ttf", 22)
mono_bold   = ImageFont.truetype(f"{FONTS}/GeistMono-Bold.ttf", 18)
mono_reg    = ImageFont.truetype(f"{FONTS}/GeistMono-Regular.ttf", 14)
mono_small  = ImageFont.truetype(f"{FONTS}/GeistMono-Regular.ttf", 12)
mono_xs     = ImageFont.truetype(f"{FONTS}/GeistMono-Regular.ttf", 11)

# Top rule (always)
draw.line([(PAD, 48), (W - PAD, 48)], fill="#000000", width=2)

# Title
draw.text((PAD, 68), "Your Title Here", font=outfit_bold, fill="#000000")
draw.text((PAD, 130), "Subtitle or tagline", font=outfit_reg, fill="#999999")

# ... your image content here ...

# Footer (always)
draw.line([(PAD, H - 44), (W - PAD, H - 44)], fill="#000000", width=1)
draw.text((PAD, H - 28),
          "arrays.org  —  Unified Data Engine for Intelligent Finance",
          font=mono_small, fill="#BBBBBB")

img.save("/mnt/user-data/outputs/filename.png", "PNG", dpi=(144, 144))
```

---

## Common Mistakes to Avoid

- ❌ Using `ı[[arrays]]ı` — always use `[[arrays]]` without pipes
- ❌ Adding any color (blue, green, etc.) — strictly black/white/grey
- ❌ Using emoji for checkmarks — use `YES` / `NO`
- ❌ Tight/cramped layouts — generous whitespace is core to the style
- ❌ Forgetting to draw connector lines before source boxes
- ❌ Saving without `dpi=(144, 144)` — results in blurry output
- ❌ Canvas width other than 1400px — breaks visual consistency across assets
