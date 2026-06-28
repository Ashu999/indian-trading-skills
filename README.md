# Indian Trading Skills for Claude

> Turn Claude into your Indian market research analyst and trade-planning assistant. 19 specialized skills covering NSE/BSE equities, F&O derivatives, institutional flows, market breadth, live news tracking, weekly trade planning, position sizing, stops, risk/reward, RSI divergence, Fibonacci levels, and multi-timeframe analysis — all built for Indian markets.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![Claude Skills](https://img.shields.io/badge/Claude-Skills-blueviolet)](https://claude.ai)

## What This Is

A collection of [Claude Skills](https://docs.anthropic.com/en/docs/claude-code/skills) that give Claude deep knowledge of Indian equity markets. Each skill is a self-contained module with methodology references, scoring frameworks, Python scripts where useful, or lightweight prompt frameworks for trade planning and risk management — purpose-built for NSE/BSE.

**No API keys required** for most skills. Uses free data sources (yfinance, niftystocks) and optionally integrates with [Groww MCP](https://groww.in) or [Zerodha Kite MCP](https://github.com/zerodha/kite-mcp-server) for live market data and trading.

Adapted from [tradermonty/claude-trading-skills](https://github.com/tradermonty/claude-trading-skills) (US markets) for Indian markets.

## Quick Start

### 1. Clone the repo

```bash
git clone https://github.com/ajeeshworkspace/indian-trading-skills.git
cd indian-trading-skills
```

### 2. Install dependencies

```bash
pip install -e .

# Or install directly
pip install yfinance pandas scipy pyyaml niftystocks feedparser
```

### 3. Add skills to Claude

**Claude Code (CLI):**
Copy the `skills/` directory into your project or point Claude to this repo. Claude automatically discovers `SKILL.md` files.

**Claude Desktop / claude.ai:**
Reference the skill files in your project knowledge or system prompt.

### 4. Start using

Just talk to Claude naturally:

```
"Analyze Reliance Industries stock fundamentals"
"Screen Nifty 500 for VCP breakout setups"
"What's the market impact if RBI cuts rates by 50 bps?"
"Build an iron condor on Bank Nifty for this week's expiry"
"How healthy is the broad market right now?"
"Evaluate my backtest — 150 trades, 58% win rate, 1.8 profit factor"
"What are FII/DII flows telling us this month?"
"Give me today's market news briefing"
"Any news about Tata Motors?"
"Use nse-trading-toolkit to plan a RELIANCE swing trade"
"Position size for HDFCBANK with Rs.5 lakh account"
"Where should I set stop-loss for INFY at Rs.1,850?"
"Is this trade worth it? Entry 1800, stop 1700, target 2100"
"Check RSI divergence on TATAMOTORS daily chart"
```

## Skills

### 1. Technical Analyst
Systematic technical analysis of weekly price charts for Indian stocks and indices. Feed it chart images and get structured analysis with probabilistic scenarios.

| | |
|---|---|
| **Trigger** | Provide chart images for analysis |
| **Output** | Trend, S/R levels, MA analysis, volume, 2-4 probability-weighted scenarios |
| **Data** | Chart images (user-provided) |

### 2. NSE VCP Screener
Screens Nifty 50/200/500 for Mark Minervini's Volatility Contraction Pattern — the setup behind many of the biggest stock moves.

| | |
|---|---|
| **Trigger** | "Screen for VCP setups", "Find breakout candidates" |
| **Output** | Ranked candidates with composite scores, pivot levels, risk metrics |
| **Data** | yfinance (free, no API key) |

**Standalone CLI usage:**
```bash
# Screen Nifty 50
python3 skills/nse-vcp-screener/scripts/screen_vcp.py --universe nifty50

# Screen Nifty 500 (broader universe)
python3 skills/nse-vcp-screener/scripts/screen_vcp.py --universe nifty500

# Custom tickers
python3 skills/nse-vcp-screener/scripts/screen_vcp.py --custom-tickers RELIANCE,TCS,INFY,HDFC

# Tune parameters
python3 skills/nse-vcp-screener/scripts/screen_vcp.py --universe nifty200 \
  --min-contractions 2 --t1-depth-min 10 --t1-depth-max 40 \
  --trend-min-score 85 --output-dir reports/
```

**Scoring system:** 5-component composite (Trend Template 25%, Contraction Quality 25%, Volume Dry-Up 20%, Pivot Proximity 15%, Relative Strength 15%).

### 3. India Stock Analysis
Deep fundamental + technical analysis of NSE/BSE stocks. Covers business quality, financials, valuation, shareholding patterns, and peer comparison.

| | |
|---|---|
| **Trigger** | "Analyze TCS", "Compare HDFC Bank vs ICICI Bank" |
| **Output** | Investment report with bull/bear cases, scorecard, risk matrix |
| **Data** | Groww MCP (live) or yfinance (free) |

**India-specific metrics:** Promoter holding/pledge analysis, FII/DII ownership trends, sector-specific ratios (NIM/NPA for banks, ANDA pipeline for pharma, utilization for IT).

### 4. Scenario Analyzer
Builds 18-month probabilistic scenarios from headlines and events. Covers RBI policy, Union Budget, crude oil, elections, monsoons, and global risk events.

| | |
|---|---|
| **Trigger** | Share a news headline or ask about event impact |
| **Output** | 3 scenarios (base/bull/bear) with probabilities, sector impact, portfolio actions |
| **Data** | Built-in reference data (no API needed) |

**Includes:** Historical pattern database (2008-2024), sector sensitivity matrix (10 sectors x 8 event types), and 4 detailed playbooks.

### 5. FII/DII Flow Tracker
Analyzes Foreign and Domestic Institutional Investor flows and their correlation with Nifty direction. Classifies flow regimes and identifies divergences.

| | |
|---|---|
| **Trigger** | "What are FII flows this month?", "Is DII absorbing FII selling?" |
| **Output** | Flow regime assessment, Nifty correlation, sector rotation signals |
| **Data** | Web search (NSDL, NSE data) + Groww MCP |

**Includes:** 4 historical case studies (COVID 2020, Ukraine 2022, Oct 2024, FY2021 rally), flow interpretation decision matrix, FII derivative position analysis.

### 6. Options Strategy Advisor
Recommends and prices options strategies for NSE F&O. Full Black-Scholes engine with Greeks, IV solver, and strategy builders.

| | |
|---|---|
| **Trigger** | "Build a straddle on Nifty", "What's the margin for iron condor?" |
| **Output** | Strategy details, Greeks, margin requirement, ASCII P/L diagram |
| **Data** | Groww MCP (live Greeks, OI, margins) |

**Standalone CLI usage:**
```bash
# Price a call option
python3 skills/options-strategy-advisor/scripts/black_scholes.py price \
  --spot 24000 --strike 24500 --expiry 7 --vol 0.15 --rate 0.065 --type call

# Calculate Greeks
python3 skills/options-strategy-advisor/scripts/black_scholes.py greeks \
  --spot 24000 --strike 24500 --expiry 7 --vol 0.15

# Solve implied volatility
python3 skills/options-strategy-advisor/scripts/black_scholes.py iv \
  --spot 24000 --strike 24500 --expiry 7 --price 120 --type call

# Build iron condor with P/L diagram
python3 skills/options-strategy-advisor/scripts/black_scholes.py strategy \
  --name iron_condor --spot 24000 --expiry 7 --vol 0.15 \
  --strikes 23500 23800 24200 24500
```

**17 strategies supported:** Covered calls, protective puts, bull/bear spreads, straddles, strangles, iron condors, iron butterflies, calendar spreads, diagonal spreads, ratio spreads.

### 7. Backtest Expert
Validates trading strategy backtests with India-specific cost modeling and overfitting detection. Scores across 5 dimensions and delivers a Deploy/Refine/Abandon verdict.

| | |
|---|---|
| **Trigger** | "Evaluate my backtest results", "Is this strategy robust?" |
| **Output** | 0-100 score, red flags, verdict, cost-adjusted metrics |
| **Data** | User-provided backtest results |

**Standalone CLI usage:**
```bash
python3 skills/backtest-expert/scripts/evaluate_backtest.py \
  --trades 150 --win-rate 0.58 --avg-win 2.5 --avg-loss 1.2 \
  --max-drawdown 15 --years 3 --parameters 4 \
  --slippage-modeled --segment delivery --format both
```

**India-specific costs modeled:** STT, stamp duty, exchange transaction charges, SEBI turnover fees, GST — for delivery, intraday, and F&O segments.

### 8. India Market Breadth
Measures internal market health beyond Nifty/Sensex using breadth indicators. Generates a Health Score (0-100) with regime classification.

| | |
|---|---|
| **Trigger** | "How's the broad market?", "Is market breadth healthy?" |
| **Output** | Health score, regime (Risk-On/Cautious/Risk-Off), divergence alerts |
| **Data** | yfinance + Groww MCP |

**5-component scoring:** Advance/Decline (25%), Stocks above 200 DMA (25%), New Highs vs Lows (20%), Sector Participation (15%), Index Divergence (15%).

### 9. India News Tracker
Fetches, categorizes, and scores Indian market news from MoneyControl, Economic Times, LiveMint, BSE/NSE filings, and SEBI circulars. Auto-detects sentiment, affected sectors, and stock mentions.

| | |
|---|---|
| **Trigger** | "Market news today", "News about Reliance", "Banking sector update", "Upcoming earnings" |
| **Output** | Daily briefing, stock-specific digest, sector roundup, earnings calendar, corporate actions |
| **Data** | RSS feeds + Web search + Groww MCP (stocks in news, volume shockers) |

**7 modes:** Daily Briefing, Stock-Specific News, Sector Roundup, Earnings Tracker, Corporate Actions, Bulk/Block Deals, Regulatory Monitor.

**Standalone CLI usage (News Tracker):**
```bash
# Daily briefing from all sources
python3 skills/india-news-tracker/scripts/news_fetcher.py

# Stock-specific news
python3 skills/india-news-tracker/scripts/news_fetcher.py --stock RELIANCE

# Sector news
python3 skills/india-news-tracker/scripts/news_fetcher.py --sector banking

# Last 7 days, high impact only
python3 skills/india-news-tracker/scripts/news_fetcher.py --days 7 --min-impact 6

# JSON output
python3 skills/india-news-tracker/scripts/news_fetcher.py --format json --output reports/news.json
```

**Auto-feeds other skills:** Headlines → Scenario Analyzer, Earnings → Stock Analysis, FII activity → Flow Tracker, Sector signals → Market Breadth.

### 10. Weekly F&O Trade Planner
Complete weekly F&O trading workflow — from macro thesis to position management. Combines news analysis, sector screening, technical confirmation, OI data, and FII/DII flows to generate a single high-conviction directional trade idea each week, then manages it with structured stop-loss tightening and partial profit booking.

| | |
|---|---|
| **Trigger** | "Plan my F&O trade for next week", "What should I trade this week?", "Weekly trade idea" |
| **Output** | Trade Card (instrument, direction, strike, entry, SL, targets), then ongoing position management |
| **Data** | Groww MCP or Zerodha Kite MCP (live) + Web search (news, FII/DII) |

**6-phase workflow:**
1. **News & Macro Scan** — identify the dominant market narrative, rate conviction 1-5
2. **Sector & Instrument ID** — screen movers, FII/DII flows, narrow to ONE instrument
3. **Direction & Technical Confirmation** — RSI, MACD, SuperTrend, OI analysis (need 4+ signals aligned)
4. **Strategy & Entry Plan** — select strategy based on conviction + IV, generate Trade Card with exact levels
5. **Execution** — place orders with GTT stop-loss and profit targets
6. **Position Management** — daily monitoring, SL tightening ladder, partial profit booking (40-50% at T1, 30% at T2, trail rest)

**Built-in risk rules:** Max 40% capital per trade, mandatory GTT stop-loss, weekend exit rule for weekly expiry, no averaging down, orphaned GTT cleanup after every exit.

### 11. NSE Trading Toolkit
Master orchestrator for short-term NSE/BSE equity trade planning. Coordinates technical setup, multi-timeframe context, RSI divergence, Fibonacci levels, position sizing, stops, trailing stops, and risk/reward into one action plan.

| | |
|---|---|
| **Trigger** | "Analyze RELIANCE", "Should I buy SBIN?", "Plan this swing trade" |
| **Output** | Quick take, scenarios, action plan, position size, stop, targets, R:R |
| **Data** | Manual prices, Groww MCP, or yfinance |

### 12. Technical Analysis
Data-driven technical analysis for NSE/BSE equities using trend, support/resistance, volume, indicator dashboards, and probability-weighted scenarios. This complements `technical-analyst`, which is focused on chart-image analysis.

| | |
|---|---|
| **Trigger** | "What's the trend on TCS?", "support resistance for INFY", "indicators for HDFCBANK" |
| **Output** | Trend read, key levels, indicator bias, bullish/bearish/sideways scenarios |
| **Data** | Manual prices, Groww MCP, or yfinance |

### 13. Position Sizing
Calculates share quantity and capital allocation from account size, stop distance, volatility, leverage, and portfolio concentration rules.

| | |
|---|---|
| **Trigger** | "How many shares should I buy?", "position size for RELIANCE", "risk per trade" |
| **Output** | Shares, capital required, risk amount, allocation percentage |
| **Data** | Account size, entry, stop, optional ATR/portfolio data |

### 14. Stop-Loss Strategies
Initial stop-loss placement using structure, ATR, support/resistance, moving averages, buffers, and hard risk limits.

| | |
|---|---|
| **Trigger** | "Where should I set stop-loss?", "protect this INFY trade" |
| **Output** | Stop method, stop price, invalidation logic, risk validation |
| **Data** | Entry price, recent structure, ATR, key levels |

### 15. Trailing Stops
Profit-protection framework using breakeven moves, ATR trail, structure trail, moving-average trail, chandelier exit, and a hybrid staged approach.

| | |
|---|---|
| **Trigger** | "How should I trail my stop?", "lock in profits", "move stop to breakeven?" |
| **Output** | Current trailing method, next stop level, adjustment rules |
| **Data** | Entry, current price, original stop, ATR, recent swing levels |

### 16. Risk-Reward Ratio
Evaluates whether a trade is worth taking based on entry, stop, target, win-rate assumptions, multi-target exits, and expected value.

| | |
|---|---|
| **Trigger** | "Is this trade worth it?", "risk reward ratio", "expected value" |
| **Output** | R:R, rupee risk/reward, verdict, minimum required win rate |
| **Data** | Entry, stop, target, position size |

### 17. Multi-Timeframe Analysis
Uses the 3-screen method: weekly bias, daily setup, and hourly/4H entry timing, with a confluence score.

| | |
|---|---|
| **Trigger** | "Weekly vs daily trend?", "multi-timeframe check", "higher timeframe bias" |
| **Output** | Weekly/daily/hourly verdicts, confluence score, trade filter |
| **Data** | Multi-timeframe candles or manually supplied levels |

### 18. RSI Divergence
Identifies regular and hidden RSI divergences, grades signal strength, and defines confirmation, entry, stop, and target logic.

| | |
|---|---|
| **Trigger** | "RSI divergence on TATAMOTORS?", "hidden divergence", "momentum divergence" |
| **Output** | Divergence type, strength, confirmation trigger, invalidation |
| **Data** | Price swings and RSI values, or candles/indicators via data tools |

### 19. Fibonacci Trading
Calculates retracement entry zones, extension targets, and confluence areas using swing high/low anchors.

| | |
|---|---|
| **Trigger** | "Fib levels for NIFTY", "Fibonacci targets", "where to enter on pullback" |
| **Output** | Retracement levels, extension targets, confluence zones |
| **Data** | Swing high, swing low, current price |

## Project Structure

```
indian-trading-skills/
├── README.md
├── LICENSE
├── pyproject.toml
├── .gitignore
└── skills/
    ├── technical-analyst/
    │   ├── SKILL.md
    │   ├── references/technical_analysis_framework.md
    │   └── assets/analysis_template.md
    ├── nse-vcp-screener/
    │   ├── SKILL.md
    │   ├── references/{vcp_methodology, scoring_system}.md
    │   └── scripts/{screen_vcp, scorer, report_generator}.py
    │       └── calculators/{trend_template, vcp_pattern, volume_pattern,
    │                        pivot_proximity, relative_strength}_calculator.py
    ├── india-stock-analysis/
    │   ├── SKILL.md
    │   ├── references/{fundamental-analysis, financial-metrics}.md
    │   └── assets/report-template.md
    ├── scenario-analyzer/
    │   ├── SKILL.md
    │   └── references/{headline_event_patterns, sector_sensitivity_matrix,
    │                    scenario_playbooks}.md
    ├── fii-dii-flow-tracker/
    │   ├── SKILL.md
    │   ├── references/{flow_analysis_methodology, flow_interpretation_guide}.md
    │   └── assets/flow_report_template.md
    ├── options-strategy-advisor/
    │   ├── SKILL.md
    │   ├── references/indian_fno_guide.md
    │   └── scripts/black_scholes.py
    ├── backtest-expert/
    │   ├── SKILL.md
    │   ├── references/{methodology, failed_tests}.md
    │   └── scripts/evaluate_backtest.py
    ├── india-market-breadth/
    │   ├── SKILL.md
    │   ├── references/breadth_methodology.md
    │   └── assets/breadth_report_template.md
    ├── india-news-tracker/
    │   ├── SKILL.md
    │   ├── references/{news_source_guide, sector_mapping, sentiment_patterns}.md
    │   ├── scripts/news_fetcher.py
    │   └── assets/daily_briefing_template.md
    ├── weekly-fno-trade-planner/
    │   └── SKILL.md
    ├── nse-trading-toolkit/
    │   └── SKILL.md
    ├── technical-analysis/
    │   └── SKILL.md
    ├── position-sizing/
    │   └── SKILL.md
    ├── stop-loss-strategies/
    │   └── SKILL.md
    ├── trailing-stops/
    │   └── SKILL.md
    ├── risk-reward-ratio/
    │   └── SKILL.md
    ├── multi-timeframe-analysis/
    │   └── SKILL.md
    ├── rsi-divergence/
    │   └── SKILL.md
    └── fibonacci-trading/
        └── SKILL.md
```

## Broker Integration

Broker-aware skills support **dual broker MCP** — use Groww, Zerodha, or both. Skills that need live market, portfolio, margin, or order data auto-detect which broker is connected and use the appropriate tools. The lightweight trade-planning skills also work with manually supplied prices and can use Groww MCP or yfinance where available.

### Zerodha Kite MCP Setup

Add to your Claude Desktop config (`~/.config/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "kite": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.kite.trade/mcp"]
    }
  }
}
```

That's it — no API keys needed. Authentication happens through Zerodha's secure login flow when you first use it.

**Zerodha Kite MCP provides 22 tools:**
- **Market Data**: `get_ltp`, `get_quotes`, `get_ohlc`, `get_historical_data`, `search_instruments`
- **Portfolio**: `get_holdings`, `get_positions`, `get_margins`, `get_profile`, `get_mf_holdings`
- **Orders**: `place_order`, `modify_order`, `cancel_order`, `get_orders`, `get_trades`, `get_order_history`
- **GTT**: `place_gtt_order`, `modify_gtt_order`, `delete_gtt_order`, `get_gtts`

For more details: [Zerodha Kite MCP GitHub](https://github.com/zerodha/kite-mcp-server) | [Setup Guide](https://zerodha.com/z-connect/featured/connect-your-zerodha-account-to-ai-assistants-with-kite-mcp)

### Groww MCP Setup

Groww MCP is available as a connector in Claude. Connect it from the MCP connectors panel.

### Data Sources

| Source | Skills | API Key | Cost |
|--------|--------|---------|------|
| [yfinance](https://github.com/ranaroussi/yfinance) | VCP Screener, Market Breadth, News Tracker, trade-planning skills | None | Free |
| [niftystocks](https://github.com/swapniljariwala/niftystocks) | VCP Screener | None | Free |
| [Groww MCP](https://groww.in) | Broker-aware skills plus trade-planning data | Via Claude | Free |
| [Zerodha Kite MCP](https://github.com/zerodha/kite-mcp-server) | Broker-aware skills (market data + trading) | Via Claude | Free |
| [feedparser](https://github.com/kurtmckee/feedparser) | News Tracker (RSS feeds) | None | Free |
| Web Search | Flow Tracker, Scenario Analyzer, News Tracker | Via Claude | Free |

### Tool Equivalence (Groww ↔ Zerodha)

| Action | Groww MCP | Zerodha Kite MCP |
|--------|-----------|------------------|
| Live price | `get_ltp` | `get_ltp` |
| Market depth | `get_quotes_and_depth` | `get_quotes` |
| Historical candles | `fetch_historical_candle_data` | `get_historical_data` |
| Search instruments | `curate_symbols` | `search_instruments` |
| Margins | `calculate_fno_margin` | `get_margins` |
| Holdings | `get_equity_portfolio_holdings` | `get_holdings` |
| Positions | `get_my_trading_positions_today` | `get_positions` |
| Place F&O order | `place_fno_order` | `place_order` |
| Fundamentals | `fetch_stocks_fundamental_data` | — (use web search) |
| OI analysis | `get_open_interest_analysis` | — (use web search) |
| Greeks | `get_greeks_for_fno_contract` | — (use Black-Scholes script) |

## Indian Market Context

| Detail | Value |
|--------|-------|
| Exchanges | NSE (primary), BSE |
| Currency | INR (all prices in rupees) |
| Trading Hours | 9:15 AM – 3:30 PM IST |
| Settlement | T+1 for equities |
| F&O Expiry | Weekly (Thu) for index options, monthly for stock options |
| Circuit Limits | 2%, 5%, 10%, 20% daily limits |
| Regulator | SEBI |
| Key Indices | Nifty 50, Sensex, Bank Nifty, Nifty IT, Nifty Pharma |

## Contributing

Contributions welcome! Some ideas:

- **New skills**: CANSLIM screener, promoter pledge monitor, IPO analyzer, F&O ban monitor, trade journal/dashboard
- **Enhancements**: Tests for existing calculators, more sector-specific analysis frameworks
- **Data sources**: Additional free data integrations (NSE API, BSE API)
- **Documentation**: Usage examples, video walkthroughs

Please open an issue first to discuss significant changes.

## Disclaimer

This project is for **educational and research purposes only**. It does not constitute financial advice. Always do your own research before making investment decisions. The authors are not SEBI-registered investment advisors.

Past performance of any strategy, indicator, or methodology does not guarantee future results. Trading in equities and derivatives involves substantial risk of loss.

## License

[MIT](LICENSE) — use it, fork it, build on it.

## Credits

- Upstream: [tradermonty/claude-trading-skills](https://github.com/tradermonty/claude-trading-skills) (US markets)
- Methodology: Mark Minervini (VCP/Trend Template), William O'Neil (CANSLIM concepts)
- Data: [Yahoo Finance](https://finance.yahoo.com), [Groww](https://groww.in), [Zerodha](https://zerodha.com), [NSE India](https://www.nseindia.com)
