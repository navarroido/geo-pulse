# GEO Pulse — Daily Pipeline Instructions

> This file is read by the cron agent every day.
> Follow these steps exactly, in order. Do not skip steps.
> **Newsletter language: ENGLISH ONLY. All content, copy, and HTML template must be in English.**

---

## Step 0 — Setup

- Today's date (Israel time): get from session_status
- Newsletter filename: `newsletters/YYYY-MM-DD.html`
- Repo local path: `/root/.openclaw/workspace/geo-pulse/`
- GitHub remote: `https://github.com/navarroido/geo-pulse`

---

## Step 1 — Load Covered History

Read `/root/.openclaw/workspace/geo-pulse/data/history.json`
Extract the list of already-covered URLs.
**Do not include any of these URLs in today's newsletter.**

---

## Step 2 — Spawn Dex to Gather News

Spawn Dex (model: gpt-4o) with this task:

```
You are Dex, a research agent specializing in AI Visibility and Generative Engine Optimization (GEO).

TODAY: [insert today's date]

Your job: Find 5 fresh, noteworthy news items from the last 48 hours about AI Visibility, GEO, and how brands/content rank in LLM responses.

Search strategy: Search the web by TOPIC, not by fixed source list. Use queries like:
- "GEO generative engine optimization [today's date]"
- "AI visibility LLMs brand mentions 2025"
- "llms.txt news [this week]"
- "Google AI Overviews update [this month]"
- "SearchGPT Perplexity Gemini visibility news"
- "AI search ranking factors new research"
- "brand mentions ChatGPT Claude Gemini study"
- "prompt engine optimization news"
- "AI search CTR visibility report"

Preferred sources (prioritize if they have fresh content):
- searchengineland.com
- searchenginejournal.com
- Kevin Indig (kevin-indig.com / Substack)
- Animalz blog
- Moz blog
- Ahrefs blog
- Semrush blog
- VentureBeat (AI section)
- The Verge (AI section)
- Google Search Central blog
- Bing Webmaster Blog
- Perplexity blog
- Anthropic blog / OpenAI blog (when relevant to search/visibility)
- brightedge.com
- contentatscale.ai blog
- Twitter/X threads by known GEO researchers (screenshot summaries)

Rules:
- Only include items published in the last 48 hours
- Skip any URL already in the covered history list (you will receive it)
- Each item must have: title, URL, 2-sentence English summary, category tag
- Categories: GEO Research / AI Search Update / Tool Launch / Industry Move / Case Study / Opinion / LLMs.txt
- Prefer: new studies, tool launches, platform changes, significant research — avoid minor blog posts
- If a topic is trending on Twitter/X without a canonical article, find the best thread or summary post

Already covered URLs (skip these):
[INSERT history URLs here]

Output format (JSON array):
[
  {
    "title": "...",
    "url": "...",
    "summary_en": "...",
    "category": "...",
    "emoji": "..."
  }
]

Return ONLY the JSON array, nothing else.
```

Wait for Dex to return 5 items.
If fewer than 3 items found — still proceed with what's available.

---

## Step 3 — Fetch OG Images

For each item URL, run:
```bash
curl -sL --max-time 8 "[URL]" | grep -o 'og:image[^>]*content="[^"]*"' | head -1
```
Extract the content="..." value as the image URL.
If no OG image found, use: `https://geo-pulse-default.png` → use this fallback instead:
`https://images.unsplash.com/photo-1677442135703-1787eea5ce01?w=1200&q=80`
(a clean AI/tech abstract image)

---

## Step 4 — Spawn Ink to Write Copy

Spawn Ink (model: gpt-4o) with this task:

```
You are Ink, a copywriter specializing in tech newsletters for marketers and SEOs.

Write sharp, engaging English newsletter copy for these AI Visibility / GEO news items.
Your audience: SEOs, content marketers, and brand managers who want to understand how to be visible in AI-generated answers.
Keep it concise, insightful, and actionable. No jargon overload.

Each item needs:
- headline_en: punchy English headline (max 12 words)
- body_en: 2-3 sentences in English, engaging and informative
- cta_en: call-to-action text (e.g. "Read more", "See the research", "Full report", "Explore the tool")

Also write a "trend_of_the_week" block:
- title_en: headline for today's main trend (max 10 words)
- body_en: 2-3 sentences about the dominant theme across today's stories — written for practitioners

Items: [INSERT DEX OUTPUT HERE]

Return ONLY a JSON object with keys matching each item's URL, plus "trend".
```

Wait for Ink to return copy.

---

## Step 5 — Build Newsletter HTML

Use the structure below. All text is in English, layout is LTR.
Save to: `/root/.openclaw/workspace/geo-pulse/newsletters/YYYY-MM-DD.html`

**Template rules:**
- LTR, English, Inter/system font stack
- lang="en" dir="ltr"
- Items 1 and 2: full-width cards with large hero image (height: 240px, object-fit: cover)
- Items 3 and 4: side-by-side half-width cards
- Item 5: horizontal card (image right, text left)
- Trend box: dark teal/indigo gradient at the bottom (GEO Pulse brand color: #0f4c75 → #1b262c)
- Issue number: count from history.json totalIssues + 1
- Header: dark gradient (indigo/teal), "GEO Pulse" title, subtitle "Your daily AI Visibility digest · [Day], [Date in English]" — no stats bar
- CTA buttons: use Ink's cta_en text
- Footer: "Issue #N · [Date] · Built by Navarro 🦅"

**Category badge colors:**
- GEO Research → #e0f2fe / #0369a1
- AI Search Update → #fef9c3 / #854d0e
- Tool Launch → #dcfce7 / #166534
- Industry Move → #fce7f3 / #9d174d
- Case Study → #ede9fe / #5b21b6
- Opinion → #f1f5f9 / #334155
- LLMs.txt → #fff7ed / #9a3412

**Base HTML structure:**
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>GEO Pulse — [DATE]</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700;900&display=swap');
    * { margin:0; padding:0; box-sizing:border-box; }
    body { background:#f0f2f5; font-family:'Inter',Arial,sans-serif; direction:ltr; }
    a { text-decoration:none; color:inherit; }
    img { display:block; max-width:100%; border:0; }
  </style>
</head>
<body>
<!-- Header: dark indigo/teal gradient, GEO Pulse branding -->
<!-- Items 1–2: full-width cards -->
<!-- Items 3–4: side-by-side cards -->
<!-- Item 5: horizontal card -->
<!-- Trend box: teal gradient -->
<!-- Footer -->
</body>
</html>
```

Build the full HTML expanding this structure with all real content from Dex + Ink.

---

## Step 6 — Update index.html

Read `/root/.openclaw/workspace/geo-pulse/index.html`
Add a new `.issue-card` entry at the TOP of the issues list (in English).
New entry format:
```html
<a href="newsletters/YYYY-MM-DD.html" class="issue-card">
  <div class="issue-left">
    <div class="issue-num">Issue #[N]</div>
    <div class="issue-title">
      <span class="latest-badge">New</span>
      [FIRST HEADLINE] &amp; [N-1] more stories
    </div>
    <div class="issue-meta">
      <span class="pill">[DATE e.g. Feb 24, 2026]</span>
      <span class="pill">[N] stories</span>
    </div>
  </div>
  <div class="arrow">→</div>
</a>
```
Remove "New" badge from the previous issue entry.

---

## Step 7 — Update history.json

Add all new items to the `covered` array.
Increment `totalIssues` by 1.
Update `lastIssue` to today's date.

---

## Step 8 — Git Commit & Push

```bash
cd /root/.openclaw/workspace/geo-pulse
git add -A
git commit -m "GEO Pulse #[N] — [YYYY-MM-DD]"
git push
```

---

## Step 9 — Notify Ido

Use the `message` tool to send a Telegram message to Ido. Always use:
- action: send
- channel: telegram
- to: 5267660109

Message body (in Hebrew):
```
🌐 GEO Pulse #[N] — [DATE in Hebrew] מוכן!

[EMOJI] [HEADLINE 1 — in English]
[EMOJI] [HEADLINE 2 — in English]
[EMOJI] [HEADLINE 3 — in English]
[EMOJI] [HEADLINE 4 — in English]
[EMOJI] [HEADLINE 5 — in English]

🔗 https://navarroido.github.io/geo-pulse/newsletters/YYYY-MM-DD.html
```

(The Telegram notification to Ido is in Hebrew — only the newsletter itself is in English.)

---

## Error Handling

- If Dex finds 0 items: send Ido a message "לא נמצאו ידיעות חדשות היום — מדלג על הגיליון"
- If git push fails: retry once, if still fails — notify Ido
- If OG image fetch fails: use the fallback Unsplash image (no crash)
- If any agent times out: proceed with what's available, note in commit message
