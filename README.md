# PULSE//AUDIT

**Offline, rule-based ad account auditor + lite marketing-mix model.** Two single-file tools:

- **`index.html` — the Audit console.** Drop in a CSV export from Meta, Google Ads, TikTok, or LinkedIn, set one target number, and get a ranked fix list — a **SCALE / KEEP / WATCH / PAUSE / KILL** verdict for every ad or keyword, with the exact math behind each call.
- **`mmm.html` — the MMM console.** Drop in a weekly spend-per-channel history and it fits a marketing-mix model (adstock carryover + diminishing-returns saturation + least squares) in your browser — measured ROI per channel, what the *next* dollar earns, and where to shift budget.

No AI. No uploads. No accounts. Everything runs in your browser, and your data never leaves the page.

## Why

Most "AI ad audit" tools read your CSV and tell you "CPA is high." The judgments a senior media buyer actually makes are deterministic: they're threshold rules over spend, conversions, frequency, and CTR. This tool encodes those rules transparently — every verdict shows the formula with your numbers plugged in, and every threshold is editable.

## Quick start

1. **[Download `index.html`](index.html)** (or clone this repo) and open it in any browser — that's the entire install.
2. Pick the platforms you advertise on (this only tailors the export walkthroughs — you can skip it).
3. Export a CSV from your ads manager (guides are built into the tool) and drop it in.
4. Set your target CPA (lead gen) or target ROAS (ecommerce).
5. Read the action queue: worst first, each row says **what to do, why, and shows the math**.

Or try it instantly with the built-in sample accounts (buttons in the tool), or the CSVs in [`samples/`](samples/).

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
| Trend alarm | 25% | Recent-half CPA up / CTR down by this → deterioration flag |
| Scale step | 20% | Suggested budget increase per round |

ROAS mode mirrors the same logic with floors instead of ceilings.

## Platform support

The column detector reads native export headers from **Meta Ads Manager** (ad/ad set/campaign level), **Google Ads** (campaigns, ad groups, ads, keywords, search terms — including report-title preamble rows and `Total:` rows), **TikTok Ads Manager**, and **LinkedIn Campaign Manager**, plus generic agency reports. Anything it misses can be mapped manually with dropdowns. It also handles UTF-16 exports, tab/semicolon delimiters, quoted thousands separators, and European decimal commas.

Only run one or two platforms? That's the normal case — the platform picker just hides the guides you don't need. Missing columns degrade gracefully (e.g. LinkedIn exports without a conversions column get creative-signal audits instead of false KILL verdicts).

## The MMM console (`mmm.html`)

The Audit console is bottom-up (which ads to fix inside one platform); the MMM console is top-down (which *channels* deserve the budget). Feed it a wide CSV — `week, meta_spend, google_spend, tv_spend, revenue` — with 6+ months of weekly rows (daily data is auto-aggregated), and it:

1. Fits **adstock** (θ grid — how much of a week's impact carries into later weeks) and **saturation** (α grid — diminishing returns) per channel by coordinate descent, then solves ordinary least squares with a non-negativity pass.
2. Reports honestly: **R² and a 20% holdout error** decide whether the split is *trustworthy / directional / too weak to act on*, flat-spend channels are marked **UNPROVEN** (a channel that never varies can't be measured), and every number has a "show the math" trace.
3. Gives verdicts on the **margin**, not the average: a channel with positive past ROI still gets **TRIM** when the *next* dollar is below break-even, and reallocation suggestions come with a start-at-10%-and-re-measure cap.

A synthetic 52-week sample with known ground truth is built in (and in [`samples/`](samples/)) — the model recovers its true channel effects, carryover, and base share, which is also how changes to the fitting code get sanity-checked.

## Privacy

The file makes zero network requests. Parsing, rules, and rendering are all client-side JavaScript; settings persist only in your browser's localStorage. Audit anything without it leaving your machine.

## Hosting it (optional)

It's a static file, so GitHub Pages works out of the box: **Settings → Pages → Deploy from branch → `main` / root**. Your copy will be live at `https://<username>.github.io/pulse-audit/`.

## Contributing

Issues and PRs welcome. The whole tool is one dependency-free HTML file — the judgment engine is the `judge()` function, header synonyms live in `SYN`, and platform export guides in `PLATFORMS`. Good first contributions: more header synonyms from real exports, new platform guides, additional rules (n-gram search-term analysis, placement breakdowns, period-over-period file comparison).

## License

[MIT](LICENSE)
