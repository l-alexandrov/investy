# Investy

A personal investing system built around six specialized Claude Code subagents (`.claude/agents/`) that share a common set of markdown files instead of a database. Talk to the main session naturally — it delegates to the right agent based on what you ask. No slash commands are needed.

## The agents

| Agent | Fires on | Job |
|---|---|---|
| `research` | "screen for...", "find me stocks that..." | Pure screener: given criteria, returns 20 tickers + short descriptions. No deep analysis. |
| `stock-analysis` | "analyze TICKER", "what do you think of TICKER" | Deep-dive on one company: 10-section report ending in a Buy/Hold/Sell + conviction rating. |
| `investment-coach` | "I want to buy/sell TICKER" | Pre-trade discipline interview (rationale, expected CAGR, risks, invalidation criteria, allocation) before you commit to a trade. |
| `investment-journal` | after a real decision, or retrospective questions | Logs the decision, updates `portfolio/holdings.md`, and answers questions like "which of my forecasts were most accurate". |
| `portfolio-review` | "review my TICKER position", monthly check-ins | Re-examines an existing holding against its original thesis; actively recommends Hold / Reduce / Re-evaluate. |
| `knowledge` | "what does my guide say about..." | Q&A against the compiled methodology guide (`knowledge/GUIDE.md`). |

## The usual workflow

1. **Screen** with `research` for candidate ideas, or go straight to a ticker you already have in mind.
2. **Analyze** with `stock-analysis` for the full workup.
3. Before actually trading, go through `investment-coach` — it interviews you and, if you confirm, hands off directly into `investment-journal` to log the decision (which also updates `portfolio/holdings.md`).
4. On a monthly cadence (or whenever something changes), run `portfolio-review` on existing holdings.
5. Anytime you want a methodology refresher, ask `knowledge`.

## File layout

```
knowledge/
  GUIDE.md      # compiled methodology guide — the shared reference for stock-analysis, portfolio-review, and knowledge
  rules.md      # your own rules, written by hand — take precedence over course/book content on conflict
  source/       # raw material you drop in: slides, exported Excel/CSV, lecture transcripts
portfolio/
  holdings.md   # auto-maintained current positions — updated by investment-journal, read by portfolio-review
analysis/       # one stock-analysis report per run: TICKER-YYYY-MM-DD.md
reviews/        # one portfolio-review report per run: TICKER-YYYY-MM-DD.md
journal/        # one entry per real decision: YYYY-MM-DD-TICKER.md
research/       # screener snapshots: YYYY-MM-DD-<criteria-slug>.md
```

## Compiling the knowledge guide

`knowledge/GUIDE.md` is **not** maintained by an agent — compiling `knowledge/source/` and `knowledge/rules.md` into it is a one-time (or occasionally re-run) task you do directly in a regular session, since the source material doesn't change often. Drop your files into `knowledge/source/`, write your own rules into `knowledge/rules.md`, then ask Claude to read everything there and write `knowledge/GUIDE.md`, organized by topic (valuation approach, moat/qualitative checklist, red flags, process rules). Your own rules take precedence over course/book content — any conflict between them should be resolved and flagged inline in the guide, e.g.:

> ⚠️ **Rule conflict:** your rule says X; the course/[source] teaches Y. Your rule takes precedence — flagging here so you can reconsider.

Re-run this only when source material or rules actually change — no agent does it automatically.
