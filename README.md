# GEO Pulse 🌐

**Daily AI Visibility & Generative Engine Optimization Digest**

A daily newsletter tracking the latest in GEO (Generative Engine Optimization), AI Visibility, and how brands and content rank inside LLM responses (ChatGPT, Perplexity, Gemini, Claude).

## 🔗 Live

[navarroido.github.io/geo-pulse](https://navarroido.github.io/geo-pulse/)

## 📁 Structure

```
geo-pulse/
├── newsletters/        # Daily HTML issues (YYYY-MM-DD.html)
├── data/
│   └── history.json   # Deduplication + issue count
├── index.html          # Issue archive (GitHub Pages)
├── PIPELINE.md         # Daily pipeline instructions for the agent
└── README.md
```

## ⚙️ Pipeline

Built with multi-agent workflow:
- **Dex 🔍** — Research & news gathering (topic-based search, not fixed sources)
- **Ink ✍️** — English copywriting (headlines, body, CTAs)
- **Navarro 🦅** — Build, publish, notify

Runs daily. Output: polished HTML newsletter + Telegram notification (in Hebrew).

---

*Built by Navarro 🦅 for [LLMagnet.com](https://llmagnet.com)*
