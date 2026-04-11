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

TODAY: April 11, 2026

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
STRICT RULE: Never use the em dash character (—) anywhere. Not in headlines, not in body copy, not in CTAs. Use a comma, period, colon, or rewrite the sentence instead.

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

Use the **V3 template** as the base design. Reference file:
`/root/.openclaw/workspace/geo-pulse/templates/newsletter-v3.html`

Read that file in full and adapt it with today's content. Do NOT invent a new layout.

Save output to: `/root/.openclaw/workspace/geo-pulse/newsletters/YYYY-MM-DD.html`

**Template V3 rules:**
- Fonts: `Playfair Display` (logo, editorial title) + `Inter` (body) — import from Google Fonts
- Language: English, `lang="en" dir="ltr"`
- Background: `#f0f3f8` body, `#060e1f` header and footer
- Accent color: `#0ea5e9` (sky blue), secondary: `#818cf8` (indigo)
- Issue number: count from `history.json` → `totalIssues + 1`
- Max width: 680px, centered

**Image handling — critical:**
- Use CSS `aspect-ratio` on the image wrapper, NEVER fixed `height`
- Story 1 (lead): `aspect-ratio: 16 / 9`
- Story 2 and 5 (standard): `aspect-ratio: 3 / 2`
- Stories 3 and 4 (compact, horizontal): `aspect-ratio: 4 / 3` on a 160px wide sidebar
- Always set `object-fit: cover` on the `<img>` inside — so images fill cleanly without distortion

**Layout structure (in order):**
1. **Header** — dark `#060e1f` bg, rainbow gradient line at bottom, Playfair Display logo with gradient text, issue pill, date
2. **Digest box** — white card with `#0ea5e9` left accent, numbered list of 5 headlines (30-second scan)
3. **Section rule** — "Top Stories"
4. **Story 1** — `.story.story-lead` — 16:9 image, large bold title, full body text, dark primary CTA button
5. **Story 2** — `.story.story-standard` — 3:2 image, standard title, body, underline CTA
6. **Section rule** — "More Stories"
7. **Story 3** — `.story.story-compact` — 160px 4:3 thumbnail left, text right
8. **Story 4** — `.story.story-compact` — same layout
9. **Story 5** — `.story.story-standard` — 3:2 image, standard layout
10. **Section rule** — "Navarro's Take"
11. **Editorial box** — dark `#060e1f` bg, Playfair Display title, decorative quote mark `"`, Ink's trend copy
12. **Footer** — dark bg, Playfair logo, links, fine print

**Category badge colors:**
- GEO Research     → `#eff6ff` / `#1d4ed8`
- AI Search Update → `#fefce8` / `#a16207`
- Tool Launch      → `#f0fdf4` / `#15803d`
- Industry Move    → `#fff0f3` / `#be123c`
- Case Study       → `#ede9fe` / `#5b21b6`
- Opinion          → `#f8fafc` / `#475569` + `1px solid #e2e8f0`
- LLMs.txt         → `#fff7ed` / `#9a3412`

**CTAs:**
- Story 1 and 5: dark button `.cta-primary` — `background: #0f172a`, white text, 10px radius
- Stories 2, 3, 4: underline link `.cta-secondary` / `.cta-sm` — `color: #0ea5e9`, bottom border `#bae6fd`

**Digest + story numbers:**
- Digest numbers: `font-weight: 800`, color `#0ea5e9`
- Story badge numbers: small `11px`, `font-weight: 800`, muted gray — except lead which is `#0ea5e9`

Build the full HTML using this design. Copy ALL CSS from the reference template, then replace content only.

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
