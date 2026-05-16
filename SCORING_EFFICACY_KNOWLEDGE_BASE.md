# Options Terminal Scoring Efficacy Knowledge Base
**Source:** RRJCAR Terminal v3.8 — 557 tickers, sandbox environment  
**Date:** May 2026 | **Universe:** S&P 500 + extended universe, options-eligible  
**Score scale:** 0–150 pts across 17 active rules | **GO threshold:** 110

---

## 1. Score Distribution

| Score band | Tickers | % of universe | Notes |
|---|---|---|---|
| 130–137 | 12 | 2.2% | Top tier — all major rules firing |
| 120–129 | 39 | 7.0% | Strong — most rules active |
| 110–119 | 47 | 8.4% | **GO threshold** — qualified entries |
| 100–109 | 39 | 7.0% | Near-miss — 1–2 rules short |
| 90–109 | 62 | 11.1% | Moderate signal |
| 70–89 | 54 | 9.7% | Weak signal |
| < 70 | 304 | 54.6% | Insufficient signal |

**Key insight:** Score distribution is right-skewed — 55% of tickers score below 70, creating a natural filter. GO threshold at 110 captures the top 17.6% of the universe (98 tickers).

---

## 2. Rule Efficacy Table

All statistics measured across 557 tickers. Rules sorted by average pts earned (contribution to score per ticker).

| Rule | Max Pts | Avg Pts | Pass Rate | Efficiency | Behaviour Class | Notes |
|---|---|---|---|---|---|---|
| RSI Accelerating | 12 | 6.89 | 57.5% | 57.4% | **Primary signal** | Best differentiator — computed from 14-period RSI slope |
| TF Aligned 2/3 | 12 | 6.33 | 53.3% | 52.8% | **Primary signal** | Real variance: 214 at 3/3, 83 at 2/3, 73 at 1/3, 187 at 0/3 |
| EMA20 > EMA50 | 10 | 5.51 | 55.1% | 55.1% | **Signal (real)** | Real EMA crossover from 207-day history — NOT a floor when computed correctly |
| MFI Bullish | 9 | 5.46 | 60.7% | 60.7% | **Primary signal** | Money Flow Index >50 — strong correlation with momentum |
| MACD Hist + | 10 | 4.94 | 49.4% | 49.4% | **Primary signal** | EMA12 − EMA26 > 0. Requires 26+ bars of history |
| Analyst Revisions ↑ | 12 | 4.37 | 36.4% | 36.4% | **FMP signal** | High value, structurally limited — only fires when FMP data available |
| Sector PF > 2 | 5 | 3.79 | 75.8% | 75.8% | **Sector filter** | Profit factor of sector ETF — broad filter |
| Price > EMA200 | 6 | 3.36 | 56.0% | 56.0% | **Trend filter** | Long-term trend — real EMA200 from 207-day history |
| CMO Positive | 7 | 3.02 | 43.1% | 43.1% | **Momentum** | Chande Momentum Oscillator — secondary momentum confirmation |
| Analyst PT Exists | 8 | 2.92 | 36.4% | 36.5% | **FMP signal** | Price target coverage — paired with Analyst Revisions |
| 52Wk Hi Prox | 10 | 2.87 | 54.2% | **28.7%** | **Continuous** | Low efficiency — continuous scale under-rewards proximity |
| Vol > Avg | 8 | 2.76 | 63.2% | **34.5%** | **Continuous** | Continuous volume ratio — unreliable in sandbox (vol=0 issue) |
| ADX > 25 | 8 | 2.64 | 40.0% | **33.0%** | **Continuous** | Trend strength — real ADX from history, range 0–81 |
| RSI 40–70 | 8 | 2.54 | 33.4% | **31.8%** | **Continuous** | Curve-scored — boundary tickers get partial credit |
| Price Accel | 7 | 1.95 | 28.5% | **27.9%** | **Continuous** | Gap-penalised — extreme moves reduce score |
| GEX Call Heavy | 6 | 1.45 | 24.2% | 24.2% | **Market struct.** | Gamma exposure — market-condition dependent |
| RS > SPY (5d) | 8 | 0.70 | 27.6% | **8.8%** | ⚠ **Underperforming** | Scale mismatch recalibrated to 0→5% (was 0→3%) |

---

## 3. Rule Classification Framework

### 3.1 Primary Signal Rules (differentiators)
These rules create meaningful separation between tickers. Pass rates of 40–65% mean they fire on strong setups but not on everything.

| Rule | Why it differentiates |
|---|---|
| RSI Accelerating | Slope of RSI change — filters stale momentum from accelerating momentum |
| TF Aligned 2/3 | Multi-timeframe confirmation — requires daily, weekly, monthly trend alignment |
| MACD Hist + | EMA crossover momentum — cleanest trend-following signal |
| MFI Bullish | Volume-weighted price direction — harder to game than price alone |
| CMO Positive | Oscillator confirmation — secondary momentum layer |

### 3.2 Continuous-Scoring Rules (gradient signal)
These rules use scaled values rather than binary pass/fail. They contribute partial points, creating score gradients within the passing universe.

| Rule | Scale | Calibration notes |
|---|---|---|
| RSI 40–70 | Curve centred at RSI 55 | Boundaries (40, 70) score ~1pt; optimal (55) scores 8pts |
| ADX > 25 | Linear 22→40 | ADX 26 = ~2pts; ADX 40 = 8pts |
| Vol > Avg | Ratio 0.8×→2.5× | Volume/AvgVol ratio — unreliable if sandbox suppresses volume |
| 52Wk Hi Prox | 85%→100% of 52wk high | Low efficiency (28.7%) — consider narrowing window |
| RS > SPY | 0→5% outperformance | Recalibrated from 0→3%; ceiling matched to where GO tickers actually sit |
| Price Accel | 0→2.5× average move | Gap-up penalty: extreme gappers score less or negative |

### 3.3 Structural Filter Rules (binary gates)
Pass rates near 100% or very low — these act as hard filters or free points depending on market conditions.

| Rule | Pass rate | Risk |
|---|---|---|
| EMA20 > EMA50 | 55.1% | ✅ Real crossover when computed from history — genuine filter in bear markets |
| Price > EMA200 | 56.0% | ✅ Real EMA200 from 207-day history — filters downtrending stocks |
| Sector PF > 2 | 75.8% | Low risk of being too permissive in bull markets |

> ⚠ **Critical implementation note:** EMA20, EMA50, and EMA200 must be computed from actual price history. If placeholder values are used (e.g. `ema20 = price × 0.99`), these rules become 100% pass-rate floors and lose all discriminating power. Require minimum 50 bars for EMA50 and 200 bars for EMA200.

### 3.4 External Data Rules (FMP-dependent)
These rules only fire for tickers with FMP data in cache. High value when they fire but structurally limited by API coverage and rate limits.

| Rule | Coverage | Plan notes |
|---|---|---|
| Analyst Revisions ↑ | 36.4% of universe | Requires FMP Starter — pre-populate cache from localStorage at scan start |
| Analyst PT Exists | 36.4% of universe | Paired with above — fetch via `/stable/price-target-consensus` |

> **Implementation pattern:** Pre-populate `G_FMP_CACHE` from localStorage before scoring loop. Without this, tickers with valid cached FMP data lose 20pts because `scoreIt` checks in-memory cache only.

---

## 4. Indicator Distributions (557 tickers)

| Indicator | Min | Max | Avg | Notes |
|---|---|---|---|---|
| RSI | 7 | 92 | 46.7 | Healthy spread — real variance from 14-period Wilder RSI |
| ADX | 0 | 81 | 25.2 | Right at the 25 threshold average — real 14-period ADX from history |
| MACD | −X | +X | — | 275 positive (49%), 282 negative (51%), 0 at exactly zero |
| TF | 0–3 | — | — | 214×3/3, 83×2/3, 73×1/3, 187×0/3 — genuine multi-TF signal |
| RS vs SPY | −X% | +X% | −0.91% | 403 negative (72%), 154 positive (28%) on this scan day |

---

## 5. Common Implementation Bugs and Fixes

### Bug 1 — Indicators as placeholders
**Symptom:** RSI always 50, ADX always 30, all EMA rules pass 100%  
**Root cause:** `ind` object built with hardcoded placeholders (`rsi:50, macd:0, ema20:price*0.99`) before history is available  
**Fix:** Compute `calcRSI`, `calcADX`, `calcMACD`, `calcEMA`, `calcTF` from price history in the hist callback; update `ind` values before calling `scoreIt`

### Bug 2 — History window too short for MACD/EMA200
**Symptom:** MACD = 0 for all tickers; EMA200 inaccurate  
**Root cause:** History fetch window of 25–60 calendar days returns only 17–42 trading bars — below the 26-bar minimum for MACD, well below 200 for EMA200  
**Fix:** Extend history window to 300 calendar days (~210 trading bars). Invalidate cached entries with fewer than 50 bars.

### Bug 3 — MACD update only in async path
**Symptom:** MACD computes correctly in isolation but shows 0 after scan  
**Root cause:** `calcMACD` was called in the async fetch callback but NOT in the cached-hit path. All tickers already in memory cache skipped the update.  
**Fix:** Apply all indicator computations (`calcMACD`, `calcRSI`, `calcADX`, `calcEMA`, `calcTF`) in BOTH the cached path AND the async fetch path before calling `scoreIt`.

### Bug 4 — Function defined inside another function's scope
**Symptom:** `calcMACD is not defined` error at runtime despite appearing in source  
**Root cause:** Function was inserted inside `calcCMO`'s body (after an early `return` statement), making it a nested function inaccessible from global scope  
**Fix:** Ensure all `calc*` functions are defined at the top-level module scope, not nested inside other functions.

### Bug 5 — FMP cache not pre-populated
**Symptom:** Analyst rules show "No FMP key" for most tickers despite localStorage having 500+ cached entries  
**Root cause:** `scoreIt` checks `G_FMP_CACHE` (in-memory) only. `loadFmpCache` is only called lazily per-ticker. Cached entries from prior sessions exist in localStorage but aren't loaded into memory.  
**Fix:** Before the scoring loop, iterate all tickers and call `loadFmpCache(ticker)` to populate `G_FMP_CACHE` from localStorage. One-time cost, milliseconds for 500 tickers.

### Bug 6 — Scale/reality mismatch on RS > SPY
**Symptom:** Rule earns avg 1.01/8pts despite strong GO tickers having 5–14% RS  
**Root cause:** Scale ceiling set at 3% (`scalePts(rs, 0, 3, 8)`) but GO tickers average 4–14% outperformance — all capped at 8pts regardless of how strong  
**Fix:** Widen scale to 0→5% (`scalePts(rs, 0, 5, 8)`). Strong outperformers now score proportionally; mid-range outperformers score less (honest). No negative impact on existing GO tickers.

---

## 6. Score Band P&L Analysis

> ⚠ **Contaminated dataset warning:** 64 of 69 scored trades were executed during a period with active bugs (double-fills, position size errors, trail stop close failures). Score-band P&L data is not yet representative of scoring quality.

| Score band | Trades | Win rate | Avg P&L | Notes |
|---|---|---|---|---|
| 130–150 | 64 | 30% | −$799 | Bug-era trades — not representative |
| 120–129 | 4 | 25% | −$144 | Too few to evaluate |
| 110–119 | 1 | 0% | −$150 | Too few to evaluate |

**Minimum sample for meaningful analysis:** 40–50 clean post-fix trades per score band. Accumulate before making scoring adjustments based on P&L correlation.

---

## 7. Scoring Architecture Principles

### 7.1 Continuous vs Binary Scoring
Binary rules (pass=full pts, fail=0pts) create cliffs — a ticker just below a threshold scores identically to one far below. Continuous rules create gradients. Recommended approach:

- **Use binary** for structural filters (EMA crossover direction, EMA200 trend) where the condition is either true or false
- **Use continuous** for magnitude-sensitive indicators (RSI level, ADX strength, RS % outperformance, volume ratio)
- **Use tiered** for multi-state signals (TF alignment: 1/3 = 4pts, 2/3 = 8pts, 3/3 = 12pts)

### 7.2 Scale Calibration
Scale ceilings must be calibrated to where GO tickers actually sit, not where a generic strong signal "should" be. Audit every continuous rule:

1. Pull the distribution of the indicator across GO tickers
2. Set the scale ceiling at the 85th–90th percentile of GO tickers
3. Review quarterly as market conditions change (bull/bear cycles shift RS and ADX distributions significantly)

### 7.3 Gap-Up Entry Risk
Forward-looking risk not captured by backward-looking indicators. Add a gap-up modifier that reduces score when `changePct > 3× avg5dChangePct`. If price is still above the open (continuation), apply a reduced penalty. If price has stalled below the open (failed gap), apply full penalty.

```
Gap modifier:
- No gap (<1.5× avg5d move):   0 penalty
- Moderate gap (1.5–3×):       −3pts if stalled, 0 if continuing  
- Strong gap (3–5× or >3%):    −7pts if stalled, −2pts if continuing
- Extreme gap (>5× or >5%):    −12pts if stalled, −6pts if continuing
```

### 7.4 FMP Data Strategy
FMP Starter plan covers: earnings calendar, dividends, price targets, analyst revisions. Technical indicators (`/stable/technical_indicator/`) return empty on Starter.

- Fetch FMP data sequentially with 300–600ms delay to respect rate limits
- Cache results in localStorage with 24hr TTL keyed by date
- Pre-populate in-memory cache from localStorage before each scan
- Only fetch for tickers scoring ≥ 70 after tech rules to conserve rate limit
- Earnings and ex-div can be fetched once per day in background — GO tickers first, then all others at 300ms intervals

### 7.5 History Requirements by Indicator

| Indicator | Minimum bars | Recommended | Source |
|---|---|---|---|
| RSI (14) | 15 | 30+ | Daily OHLCV |
| MACD (12/26) | 26 | 60+ | Daily close |
| ADX (14) | 28 | 60+ | Daily OHLCV (needs high/low) |
| EMA20 | 20 | 50+ | Daily close |
| EMA50 | 50 | 100+ | Daily close |
| EMA200 | 200 | 210+ | Daily close |
| TF Alignment | 20 (daily) | 200 (for monthly agg) | Daily close → aggregate |
| RSI Accelerating | 20 | 40+ | Daily close |
| MFI | 14 | 30+ | Daily OHLCV + volume |
| CMO | 12 | 30+ | Daily close |

**Tradier production API:** Supports arbitrary start dates — request 300 calendar days to guarantee 200+ trading bars for EMA200. Sandbox returns same data.

---

## 8. Suggested Scoring Improvements

### 8.1 EMA Efficiency (52Wk Hi Prox)
Current efficiency: 28.7%. The rule scores proximity to 52-week high on a continuous scale from 85%→100%. Consider:
- Narrowing to 90%→100% (tighter proximity requirement)
- Or converting to tiered: >95% = 10pts, >90% = 7pts, >85% = 4pts, else 0

### 8.2 RS > SPY Recalibration
Scale widened from 0→3% to 0→5%. Average pts rose from 1.01 to 0.70 (gradient is now honest but lower). Long-term: consider using 20-day RS rather than 5-day to reduce single-session noise.

### 8.3 GEX as Confirmation Not Primary
GEX Call Heavy (18% pass rate, 6pts) is highly market-condition dependent. In trending markets it rarely fires; in volatile markets it fires broadly. Consider using as a score **bonus** (adds pts when present) rather than a primary rule (penalises absence).

### 8.4 Vol > Avg in Sandbox
Sandbox Tradier bulk quotes return `volume=0` for many tickers, making Vol > Avg unreliable. In production this should recover. Track pass rate after switching to live API — if it exceeds 60% consistently, the current weight (8pts) is appropriate.

---

## 9. Template: Scoring Analysis Query

When analysing a scoring system, extract and report:

```
1. Rule efficacy table (maxPts, avgPts, passRate, efficiency) across full universe
2. Score distribution (10-point buckets from 0 to max)
3. GO threshold and what % of universe qualifies
4. Indicator distributions (min/max/avg for each continuous indicator)
5. Floor rule check: any rule with >90% pass rate is a floor, not a signal
6. Scale calibration check: any continuous rule with efficiency <30% has a scale mismatch
7. Dead rule check: any rule with <5% pass rate or 0 avg pts is providing no signal
8. Score-band P&L (minimum 40 trades per band before drawing conclusions)
9. Correlation between top-3 scoring rules and winning trades
```

---

*Generated from RRJCAR Terminal v3.8 live scan data — May 2026*

---

## §11 — Cross-Repo Findings (added 2026-05-16)

During a parallel audit of the Panda scanner (a sister tool with shared architectural DNA), three bugs were investigated. Status in rrjcar:

### PROVEN ticker rule missing `pts` field
**Status:** CONFIRMED PRESENT in rrjcar at `index.html` line 2425.

Verbatim source line:
```js
if(provenOk){ score += 8; techScore += 8; res.push({n:'PROVEN ticker', pass:true, val:'+8pts', filter:false}); }
```
No `pts:` field in the rule object. The `+8` is applied inline to `score` and `techScore` correctly, but audit expressions like `rules.filter(r=>r.pass).map(r=>r.pts).reduce(sum)` see `undefined` for this rule, making `score - sumPassPts = +8` for every PROVEN ticker.

**Panda fix:** Added `pts:8` to the rule push — one character change. Score values are unchanged; only the metadata is corrected.

**Recommended action for rrjcar:** Apply the same fix. Change line 2425 to:
```js
if(provenOk){ score += 8; techScore += 8; res.push({n:'PROVEN ticker', pass:true, val:'+8pts', filter:false, pts:8}); }
```
**Impact estimate:** rrjcar PROVEN whitelist has **15 tickers** (URI, C, GOOG, GS, JPM, HAL, VLO, MPC, ICE, CME, FTNT, ORCL, PANW, KO, RCL). Each of these 15 tickers has `score - sumOfPassingRulePts = +8` due to the missing pts field.

---

### Broken Trend penalty sign-flip
**Status:** NOT PRESENT in rrjcar. Zero matches for `Broken Trend`, `isPenalty`, or any `rl.pts`-based score-summing loop. rrjcar has no Broken Trend rule and no penalty-class rules at all. The only score deductions are the AVOID penalty (`score -= 15` inline at line 2424) and the side-channel Weak Sector deduction (if applicable).

**Panda fix:** Changed the Broken Trend rule's `pts` from `-5` to `0` (score deduction is applied inline, not through the pts field). Also fixed the `avoidBad` sign-flip in `reclassifyGoThreshold` that was reading `rule.pass && rule.pts < 0` instead of checking the AVOID list directly.

**Recommended action for rrjcar:** No action needed. rrjcar's simpler rule set avoids this class of bug.

---

### SPY history pre-fetch for relStr21d
**Status:** N/A — different architecture. rrjcar has no `G_HIST_CACHE`, no `fetchIndicatorHistory`, and no `relStr21d` computation. SPY appears only as a dynamic universe entry (injected at line 2676: `if(!seen['SPY']) allTickers.push({t:'SPY', s:'ETF'})`) and as a quote symbol for index display. There is no SPY-relative 21-day RS rule in rrjcar's scoreIt.

**Panda fix:** Added a fire-and-forget `fetchIndicatorHistory('SPY')` call before the foreground enrichment queue starts, so `G_HIST_CACHE['SPY']` is available to the relStr21d computation in scoreIt regardless of SPY's rank in results.

**Recommended action for rrjcar:** No action needed. If rrjcar adds a SPY-relative RS rule backed by indicator history in the future, carry this pre-fetch pattern over at that time.

---

These findings emerged from the May 2026 Panda fix cycle (PRs #45 and #46 on the scanner repo). For full context, see `REFACTOR_INVENTORY.md` on the scanner repo.

*Addendum added 2026-05-16*
