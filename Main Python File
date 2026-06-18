import anthropic
import yfinance as yf
import streamlit as st

# --- Page Config ---
st.set_page_config(page_title="AI Stock Analyzer", page_icon="📈", layout="centered")
st.title("📈 AI Stock Analyzer")
st.caption("Enter a company ticker symbol to get an AI-powered financial summary.")

# --- Input ---
ticker_input = st.text_input("Enter a stock ticker (e.g. AAPL, TSLA, MSFT)", max_chars=10)

if st.button("Analyze") and ticker_input:
    ticker = ticker_input.upper().strip()

    with st.spinner(f"Fetching data for {ticker}..."):

        # --- Pull Stock Data ---
        try:
            stock = yf.Ticker(ticker)
            info = stock.info

            company_name = info.get("longName", ticker)
            sector = info.get("sector", "N/A")
            industry = info.get("industry", "N/A")
            market_cap = info.get("marketCap", "N/A")
            pe_ratio = info.get("trailingPE", "N/A")
            revenue = info.get("totalRevenue", "N/A")
            profit_margin = info.get("profitMargins", "N/A")
            summary = info.get("longBusinessSummary", "No description available.")

            stock_data_text = f"""
Company: {company_name}
Ticker: {ticker}
Sector: {sector}
Industry: {industry}
Market Cap: {market_cap}
P/E Ratio: {pe_ratio}
Total Revenue: {revenue}
Profit Margin: {profit_margin}
Business Summary: {summary}
"""
        except Exception as e:
            st.error(f"Could not fetch data for '{ticker}'. Double-check the ticker symbol.")
            st.stop()

    with st.spinner("Asking AI to analyze..."):

        # --- Ask Claude ---
        client = anthropic.Anthropic()

        message = client.messages.create(
            model="claude-sonnet-4-6",
            max_tokens=1024,
            messages=[
                {
                    "role": "user",
                    "content": f"""You are a friendly financial analyst. Analyze the following company data and give a clear, plain-English summary that a college student could understand.

Include:
1. What this company does (1-2 sentences)
2. How healthy the financials look (simple explanation)
3. Key strengths
4. Key risks
5. A simple overall verdict: Looks Strong / Looks Mixed / Looks Weak — and why

Here is the data:
{stock_data_text}
"""
                }
            ]
        )

        analysis = message.content[0].text

    # --- Display Results ---
    st.subheader(f"Analysis for {company_name} ({ticker})")

    col1, col2, col3 = st.columns(3)
    col1.metric("Sector", sector)
    col2.metric("P/E Ratio", pe_ratio)
    col3.metric("Profit Margin", f"{round(float(profit_margin) * 100, 1)}%" if profit_margin != "N/A" else "N/A")

    st.divider()
    st.markdown(analysis)
