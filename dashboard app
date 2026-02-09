import streamlit as st
import numpy as np
from sklearn.linear_model import LinearRegression
import matplotlib.pyplot as plt

st.set_page_config(page_title="Financial Intelligence Dashboard", layout="wide")

st.title("Financial Intelligence Dashboard")

st.sidebar.header("Financial Inputs")

revenue_input = st.sidebar.text_input(
    "Revenue (comma separated)",
    "100,120,140,160,180,200"
)
cogs_input = st.sidebar.text_input(
    "COGS (comma separated)",
    "40,48,56,64,72,80"
)
opex_input = st.sidebar.text_input(
    "Operating Expenses (comma separated)",
    "30,32,35,38,40,42"
)

debt = st.sidebar.number_input("Total Debt", value=200.0)
equity = st.sidebar.number_input("Equity", value=300.0)
cash = st.sidebar.number_input("Cash Balance", value=120.0)

scenario = st.sidebar.selectbox(
    "Scenario",
    ["Base Case", "Stress Case", "Upside Case"]
)

def parse_series(text):
    return np.array([float(x.strip()) for x in text.split(",")])

revenue = parse_series(revenue_input)
cogs = parse_series(cogs_input)
opex = parse_series(opex_input)

scenario_factor = {
    "Base Case": 1.0,
    "Stress Case": 0.85,
    "Upside Case": 1.15
}[scenario]

gross_margin = (revenue[-1] - cogs[-1]) / revenue[-1]
operating_margin = (revenue[-1] - cogs[-1] - opex[-1]) / revenue[-1]
debt_to_equity = debt / equity
cash_runway = cash / opex[-1]

def forecast(values, periods=6, factor=1.0):
    X = np.arange(len(values)).reshape(-1, 1)
    y = values
    model = LinearRegression()
    model.fit(X, y)
    future_X = np.arange(len(values), len(values) + periods).reshape(-1, 1)
    return model.predict(future_X) * factor

revenue_forecast = forecast(revenue, factor=scenario_factor)
profit = revenue - cogs - opex
profit_forecast = forecast(profit, factor=scenario_factor)

st.subheader("Executive Overview")

c1, c2, c3, c4 = st.columns(4)

c1.metric(
    "Revenue Growth",
    f"{((revenue[-1] / revenue[-2]) - 1) * 100:.1f}%"
)
c2.metric(
    "Operating Margin",
    f"{operating_margin * 100:.1f}%"
)
c3.metric(
    "Cash Runway",
    f"{cash_runway:.1f} months"
)
c4.metric(
    "Debt / Equity",
    f"{debt_to_equity:.2f}"
)

st.subheader("Performance Analysis")

fig1, ax1 = plt.subplots()
ax1.plot(revenue, label="Revenue", linewidth=2)
ax1.plot(profit, label="Profit", linewidth=2)
ax1.set_title("Revenue vs Profit")
ax1.legend()
st.pyplot(fig1)

st.subheader("Forecast & Scenario Analysis")

fig2, ax2 = plt.subplots()
ax2.plot(
    np.concatenate([revenue, revenue_forecast]),
    label="Revenue Forecast",
    linewidth=2
)
ax2.plot(
    np.concatenate([profit, profit_forecast]),
    label="Profit Forecast",
    linewidth=2
)
ax2.axvline(len(revenue) - 1, linestyle="--", color="gray")
ax2.set_title(scenario)
ax2.legend()
st.pyplot(fig2)

st.subheader("Insights")

if operating_margin < 0.15:
    st.warning("Growth is margin-dilutive at current cost structure.")

if cash_runway < 9:
    st.error("Liquidity risk: cash exhaustion within 9 months.")

if debt_to_equity > 1:
    st.info("Capital structure is debt-heavy.")
