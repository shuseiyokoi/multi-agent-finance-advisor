
---
# Finance Multi-Agent Analyzer

This project is a **multi-agent pipeline** for analyzing stocks with live market data, recent news, and AI-generated summaries.  
It uses [LangGraph](https://github.com/langchain-ai/langgraph) to orchestrate agents that:

1. **Parse user questions** (tickers, company names, date ranges).
2. **Resolve symbols** from company names (via Yahoo Finance search API, optional MCP browsing).
3. **Fetch price data** (Yahoo Finance “chart v8” API, fallback to `yfinance`).
4. **Collect research** (MCP web search/scraping, fallback to Yahoo Finance RSS feeds).
5. **Compute analysis** (returns, volatility, drawdown, moving averages).
6. **Visualize charts** (with moving averages, saved as PNG).
7. **Generate a narrative report** (metrics + news highlights + disclaimer).

---

## Features

- **Flexible queries:** Ask in natural language, e.g.
  - “Analyze Apple from Jan 1, 2025 to Aug 31, 2025”
  - “Compare Tesla and NVIDIA for last 6 months”
- **Ticker resolution:** Converts company names into ticker symbols dynamically.
- **Market data:** Reliable OHLCV series via Yahoo Finance’s public API or `yfinance` fallback.
- **Research integration:**
  - **MCP**: If you configure MCP servers (e.g. Brave Search, Playwright browser).
  - **RSS fallback**: Yahoo Finance RSS feeds (no API key required).
- **Analysis metrics:**
  - Period return
  - Annualized volatility
  - Maximum drawdown
  - Moving average trend check
- **Visuals:** Charts with price + MA20/MA50 lines, auto-saved to disk.
- **Reports:** Human-readable summaries including highlights from recent articles.

---

## Requirements

- Python 3.9+
- Recommended to use a virtual environment.

### Install dependencies

```bash
pip install langgraph requests pandas matplotlib yfinance python-dateutil
pip install feedparser beautifulsoup4 lxml html5lib     # for RSS fallback
pip install langchain-mcp-adapters mcp anyio            # for MCP browsing (optional)
pip install openai                                      # for LLM intent parsing (optional)
````

---

## Usage

Clone the repo and run the script:

```bash
python finance_multiagent_no_events_fixed_order.py
```

Example output:

* **Report text** printed to console
* **Charts** saved locally as PNG (e.g. `AAPL_chart.png`, `TSLA_chart.png`)

---

## Example Prompt & Response

**Prompt:**

```text
Analyze Nvidia from Jan 1, 2023 to Aug 31, 2025
```

<img width="571" height="438" alt="NVDA_chart" src="https://github.com/user-attachments/assets/2dfdf53a-8b86-4fc0-96ab-186895fdfda1" />

**Response (sample run):**



```text
**Resolved companies → tickers:** Nvidia → NVDA

**NVDA** — Last price ~ 177.33 USD. Window 2023-01-01→2025-08-31: total return ≈ 1072.2%, annualized volatility ≈ 48.7%, max drawdown ≈ -36.1%. Trend check: 20-day MA < 50-day MA.
**Research highlights (recent):**
- Dow Jones Futures: Nvidia, GE Vernova Lead New Buys As Oracle Drives AI Stocks — Dow Jones Futures: Nvidia, GE Vernova Lead New Buys As Oracle Drives AI Stocks Oops, something went wrong Skip to navigation Skip to main content Skip to right  (https://finance.yahoo.com/m/64a96e4d-a748-374d-8efd-5ccbccb308cb/dow-jones-futures%3A-nvidia%2C-ge.html?.tsrc=rss)
- Taiwan Semiconductor Marches Higher On August Sales Report — Taiwan Semiconductor Marches Higher On August Sales Report Oops, something went wrong Skip to navigation Skip to main content Skip to right column News Today's  (https://finance.yahoo.com/m/c852d49d-59a3-3d6e-a2d5-f181f46976c2/taiwan-semiconductor-marches.html?.tsrc=rss)
- S&P 500 Closes at New High Ahead of Consumer Inflation Data; Oracle Surges — S&P 500 Closes at New High Ahead of Consumer Inflation Data; Oracle Surges Oops, something went wrong Skip to navigation Skip to main content Skip to right colu (https://finance.yahoo.com/news/p-500-closes-high-ahead-205719263.html?.tsrc=rss)
- AI Is Surging to Even Greater Heights. Oracle Is Just 1 Sign. — AI Is Surging to Even Greater Heights. Oracle Is Just 1 Sign. Oops, something went wrong Skip to navigation Skip to main content Skip to right column News Today (https://finance.yahoo.com/m/1a964900-3feb-3ada-9bd2-a4aaa0a39f82/ai-is-surging-to-even-greater.html?.tsrc=rss)
- Oracle May Be Having Its Nvidia Moment. Or It Could Be a Repeat of 1999. — Oracle May Be Having Its Nvidia Moment. Or It Could Be a Repeat of 1999. Oops, something went wrong Skip to navigation Skip to main content Skip to right column (https://finance.yahoo.com/m/bb9b2d31-e214-32a8-bf77-d06757e732ae/oracle-may-be-having-its.html?.tsrc=rss)

_Note: Educational analysis, not investment advice. Yahoo endpoints are unofficial and may change. External links are for reference—verify with primary sources._
{'NVDA': 'NVDA_chart.png'}
/var/folders/q6/4k3737d52zb9_4wzscv_sx100000gn/T/ipykernel_70149/220897417.py:54: FutureWarning: The default fill_method='pad' in Series.pct_change is deprecated and will be removed in a future version. Either fill in any non-leading NA values prior to calling pct_change or specify 'fill_method=None' to not fill NA values.
  ret = df["close"].pct_change().dropna()
```

---

## Configuration

* **MCP integration:**
  Edit `MCP_SERVERS` in the script to configure your MCP servers, e.g.:

  ```python
  MCP_SERVERS = {
      "search": {"command": "npx", "args": ["brave-search-mcp@latest"]},
      "browser": {"command": "npx", "args": ["@djyde/mcp-browser@latest"]}
  }
  ```

* **LLM intent parsing:**
  If you set `OPENAI_API_KEY` in your environment, `_llm_extract` will use OpenAI’s API to parse questions more accurately. Otherwise, a regex-based heuristic parser is used.

* **Fallbacks:**

  * Price data: Yahoo Finance chart API → `yfinance` fallback.
  * Research: MCP web research → Yahoo Finance RSS fallback.

---

## Roadmap

* Add **event annotations** (earnings/news) directly onto charts.
* Generate **PDF reports** combining charts + text.
* Add **sentiment analysis** on research snippets.
* Support **batch analysis** for watchlists.
* Deploy as an **API service** or lightweight web app.

---

## Disclaimer

This project is for **educational purposes only**.
It is **not financial advice**. Data sources (Yahoo Finance, RSS feeds, etc.) are **unofficial** and may change or break at any time.
Always verify results with primary sources before making investment decisions.

---
