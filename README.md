# PULSE//AUDIT

**Offline, rule-based ad account auditor.** Drop in a CSV export from Meta, Google Ads, TikTok, or LinkedIn, set one target number, and get a ranked fix list — a **SCALE / KEEP / WATCH / PAUSE / KILL** verdict for every ad or keyword, with the exact math behind each call.

No AI. No uploads. No accounts. One HTML file — everything runs in your browser, and your data never leaves the page.

**[▶ Open the tool](https://riz1k9.github.io/pulse-audit/)** · companion: **[PULSE//MMM](https://github.com/riz1k9/pulse-mmm)** for cross-channel budget splits.

## Why

Most "AI ad audit" tools read your CSV and tell you "CPA is high." The judgments a senior media buyer actually makes are deterministic: they're threshold rules over spend, conversions, frequency, and CTR. This tool encodes those rules transparently — every verdict shows the formula with your numbers plugged in, and every threshold is editable.

## Quick start

1. **[Download `index.html`](https://raw.githubusercontent.com/riz1k9/pulse-audit/main/index.html)** (right-click → Save Link As), or clone this repo, and open it in any browser — that's the entire install.
2. Pick the platforms you advertise on (this only tailors the export walkthroughs — you can skip it).
3. Export a CSV from your ads manager (guides are built into the tool) and drop it in.
4. Set your target CPA (lead gen) or target ROAS (ecommerce).
5. Read the action queue: worst first, each row says **what to do, why, and shows the math**.

Or try it instantly with the built-in sample accounts (the two buttons in the tool). The same files are in [`samples/`](samples/) if you'd rather practise the drag-and-drop flow or see the expected column layout.

## What you get

| Output | What it tells you |
|---|---|
| **Wasted spend identified** | Money going to ads past the kill line or over your CPA ceiling — the number to bring to your boss/client |
| **Action queue** | Every entity ranked worst-first with verdict, confidence level, plain-English reason, concrete action, and an expandable formula trace |
| **Creative signals** | Fatigue flags (frequency over cap), weak-creative flags (CTR under half your account median), CTR-decay detection |
| **Trend alarms** | If your export has a date column: entities whose CPA is rising or CTR is decaying period-over-period |
| **Budget reallocation** | How much the losers free up and where to move it, capped at a safe per-round scale step |
| **Exports** | Action queue as CSV, or a client-ready text summary for Slack/email |

## The rules (all editable in the UI)

| Rule | Default | What it does |
|---|---|---|
| Judgment floor | $40 spend | Below this: LOW DATA, no verdict |
| Kill multiple | 2× target CPA | Zero conversions past this spend → KILL |
| CPA tolerance | 1.3× | Above target×1.3 → PAUSE; between target and ceiling → WATCH |
| Scale threshold | 0.85× | CPA ≤ 85% of target with volume → SCALE |
| Min conversions to scale | 3 | Don't scale on luck |
| Frequency cap | 3.5 | Above → creative-fatigue flag |
| Low-CTR ratio | 0.5× median | Below half your account's median CTR → weak-creative flag |
| Min impressions | 1,000 | Impressions needed before any CTR judgment counts |
| Trend alarm | 25% | Recent-half CPA up / CTR down by this → deterioration flag |
| Scale step | 20% | Suggested budget increase per round |

ROAS mode mirrors the same logic with floors instead of ceilings.

## Platform support

The column detector reads native export headers from **Meta Ads Manager** (ad/ad set/campaign level), **Google Ads** (campaigns, ad groups, ads, keywords, search terms — including report-title preamble rows and `Total:` rows), **TikTok Ads Manager**, and **LinkedIn Campaign Manager**, plus generic agency reports. Anything it misses can be mapped manually with dropdowns. It also handles UTF-16 exports, tab/semicolon delimiters, quoted thousands separators, and European decimal commas.

Only run one or two platforms? That's the normal case — the platform picker just hides the guides you don't need. Missing columns degrade gracefully (e.g. LinkedIn exports without a conversions column get creative-signal audits instead of false KILL verdicts).

## Companion tool

This console is bottom-up: which ads and keywords to fix *inside* one platform. [**PULSE//MMM**](https://github.com/riz1k9/pulse-mmm) is the top-down half — a browser-based marketing-mix model that measures which *channels* deserve the budget and what the next dollar earns in each. Same philosophy: one file, no AI, transparent math.

## Privacy

The file makes zero network requests. Parsing, rules, and rendering are all client-side JavaScript; settings persist only in your browser's localStorage. Audit anything without it leaving your machine.

## Hosting it (optional)

It's a static file, so GitHub Pages works out of the box: **Settings → Pages → Deploy from branch → `main` / root**. Your copy will be live at `https://<username>.github.io/pulse-audit/`.

## Contributing

Issues and PRs welcome. The whole tool is one dependency-free HTML file — the judgment engine is the `judge()` function, header synonyms live in `SYN`, and platform export guides in `PLATFORMS`. Good first contributions: more header synonyms from real exports, new platform guides, additional rules (n-gram search-term analysis, placement breakdowns, period-over-period file comparison).

## License

[MIT](LICENSE)
