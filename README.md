# 🚚 Supply Chain Performance Dashboard
## 📌 Purpose
This dashboard was built to demonstrate how supply chain data can be transformed into actionable business intelligence. The goal was to go beyond simply visualizing numbers — and instead build a decision-driving tool that helps operations, planning, and leadership teams identify problems, understand root causes, and take targeted action.

The dataset contains **16 supply chain variables** across **100 periods** covering demand forecasting, inventory management, order fulfilment, delivery performance, supplier lead times, and cost efficiency. From these 16 variables, **22+ KPIs** were derived using Excel formulas before being connected to Tableau for visualization.

---

## 📊 Dataset — Raw Variables

The foundation of this project is a flat table with one row per period and 16 base columns:

| Column | Description |
|--------|-------------|
| `Period` | Time period index (1–100) |
| `Actual_Demand` | Real customer demand in units |
| `Forecast_Demand` | Predicted demand units |
| `Sales_Volume` | Units actually sold |
| `Average_Inventory` | Average stock held during the period |
| `Inventory_Turnover` | How often inventory is sold and replaced |
| `Days_Inventory_On_Hand` | Days of stock remaining at current demand |
| `Stockout_Rate_%` | % of demand that could not be met |
| `Order_Fulfilment_Rate_%` | % of orders successfully completed |
| `On_Time_Delivery_%` | % of deliveries arriving on schedule |
| `Backorder_Units` | Units delayed beyond promised delivery date |
| `Order_Cycle_Time_Days` | Days from order placement to delivery |
| `Supplier_Lead_Time_Days` | Days from supplier order to receipt |
| `Lead_Time_Variability_Days` | Fluctuation in supplier lead time |
| `Throughput_Time_Days` | Total time a unit spends in the supply chain |
| `Supply_Chain_Cost_per_Unit` | End-to-end cost per unit |
| `Capacity_Utilisation_%` | % of total operational capacity being used |

---

## 🧮 Calculated Fields Created
All calculated fields were built from scratch to derive deeper insights from the 16 raw variables. Every formula is documented below so the logic is fully transparent and reproducible.

### 🔵 Demand Planning

**Forecast Error**
Measures the raw gap between what was forecasted and what actually happened. A positive number means we under-forecast; negative means we over-forecast.
= Actual_Demand - Forecast_Demand

**Forecast Accuracy %**
Converts the error into a percentage accuracy score. A score of 100% means a perfect forecast. Scores below 0% indicate the forecast was off by more than the actual demand — these are the extreme outlier periods.

= IF(Actual_Demand = 0, "", (1 - ABS(Actual - Forecast) / Actual) × 100)

**MAPE Per Period**
Mean Absolute Percentage Error calculated at the individual period level. This is later averaged across all periods to produce the overall MAPE summary metric.

= IF(Actual_Demand = 0, "", ABS(Actual - Forecast) / Actual × 100)

**Forecast Bias**
Identifies whether the forecasting model systematically over-predicts or under-predicts. A consistently positive bias means the model always forecasts too high.

= Forecast_Demand - Actual_Demand

---

### 🟢 Inventory Management

**Demand Coverage Ratio**
Shows how many periods of demand the current average inventory can cover. A ratio below 1 means inventory is critically low.

= Average_Inventory / Actual_Demand

**Stockout Flag**
A binary indicator — 1 if a stockout occurred in that period, 0 if stock was available. Used to count and filter stockout events across the dashboard.

= IF(Stockout_Rate_% > 0, 1, 0)

**Inventory Health Score**
Converts the stockout rate into a positive health score out of 100. A score of 100 means zero stockouts; a score of 90 means 10% of demand went unmet.

= (1 - Stockout_Rate_% / 100) × 100

---

### 🟣 Order Fulfilment

**Backorder Rate %**
Expresses backorder units as a percentage of total demand. High backorder rates signal that the fulfilment process cannot keep up with demand.

= Backorder_Units / Actual_Demand × 100

**Lost Sales Units**
Estimates the number of units that were demanded but could not be sold due to stockouts. This directly translates to lost revenue.

= Actual_Demand × (Stockout_Rate_% / 100)
**Service Level %**
The complement of stockout rate — represents the percentage of demand that was successfully met from available stock.
= 100 - Stockout_Rate_%

**Unfulfilled Demand Units**
The raw gap between what customers wanted and what was actually sold. Captures both stockouts and fulfilment failures.
= Actual_Demand - Sales_Volume

---

### 🟡 Delivery Performance

**Late Delivery Rate %**
The direct inverse of On-Time Delivery %. Every percentage point here represents customer experience damage.
= 100 - On_Time_Delivery_%

**Order Cycle Efficiency**
Compares supplier lead time to total order cycle time. A ratio below 1.0 means the supplier delivers faster than the internal order cycle — the bottleneck is internal, not the supplier.
= Supplier_Lead_Time_Days / Order_Cycle_Time_Days

**Delivery Reliability Score**
Measures how consistent the supplier is. High variability relative to lead time produces a low score. A score above 80 indicates a dependable supplier.
= (1 - Lead_Time_Variability_Days / Supplier_Lead_Time_Days) × 100

**Lead Time Ratio**
A quick comparison of how supplier lead time stacks up against the full order cycle time. Used as a diagnostic ratio in scatter analysis.
= Supplier_Lead_Time_Days / Order_Cycle_Time_Days

---

### 🔴 Supplier & Lead Time

**Supplier Responsiveness Index**
The inverse of lead time — a higher index means faster supplier response. Used to rank and compare supplier performance across periods.
= 1 / Supplier_Lead_Time_Days

**Supply Chain Agility Index**
Measures how variable the lead time is relative to its average length. A lower index means more predictable supply — low agility index is better.
= Lead_Time_Variability_Days / Supplier_Lead_Time_Days

**Lead Time Efficiency %**
Shows what proportion of the total throughput time is consumed by the supplier lead time. High values mean the supplier is the bottleneck in the chain.
= (Supplier_Lead_Time_Days / Throughput_Time_Days) × 100

---

### 🟠 Cost & Capacity

**Total SC Cost**
The total supply chain expenditure for each period — cost per unit multiplied by the volume actually sold.
= Supply_Chain_Cost_per_Unit × Sales_Volume

**Cost per Fulfilled Unit**
Adjusts the unit cost upward to reflect fulfilment failures. If only 90% of orders are fulfilled, the real cost per successful delivery is higher than the headline cost per unit.
= Supply_Chain_Cost_per_Unit / (Order_Fulfilment_Rate_% / 100)

**Capacity Slack %**
The unused portion of operational capacity. High slack means idle resources and wasted fixed costs; too little slack means the operation is stretched thin.
= 100 - Capacity_Utilisation_%

**Cost Efficiency Index**
Units sold per unit of supply chain cost. A rising index over time means the operation is becoming more efficient — selling more for less.
= Sales_Volume / Supply_Chain_Cost_per_Unit

---

### ⭐ Composite KPIs

**Perfect Order Rate %**
The gold standard supply chain KPI. An order is only "perfect" if it is fulfilled correctly, delivered on time, and no stockout occurred. All three conditions must be met simultaneously — which is why this number is always lower than any individual metric.
= (Order_Fulfilment_Rate_% / 100)
× (On_Time_Delivery_% / 100)
× (1 - Stockout_Rate_% / 100)
× 100

**Cash-to-Cash Cycle Time (Days)**
The number of days between paying the supplier and collecting cash from the customer. A shorter cycle means faster cash recovery and better working capital efficiency.
= Days_Inventory_On_Hand + Order_Cycle_Time_Days - Supplier_Lead_Time_Days

**Period Category**
Groups the 100 periods into four quarters for trend comparison. Q1 = periods 1-25, Q2 = 26-50, Q3 = 51-75, Q4 = 76-100.
= IF(Period <= 25, "Q1",
IF(Period <= 50, "Q2",
IF(Period <= 75, "Q3", "Q4")))

---

### ⚙️ Tableau Calculated Fields

These five fields were created directly inside Tableau using Analysis → Create Calculated Field:

**Cost Flag**
Segments each period into a cost performance band for color-coded visualization in the SC Cost Analysis chart.
IF [Total SC Cost] > 3000 THEN "High"
ELSEIF [Total SC Cost] > 1500 THEN "Medium"
ELSE "Low"
END

**OTD vs Target**
Measures the gap between actual On-Time Delivery % and the 95% industry benchmark. Positive = above target; negative = below target.
[On Time Delivery %] - 95

**SC Performance Band**
Converts the numeric Overall SC Score into a readable performance label for executive-level reporting.
IF [Overall SC Score] >= 90 THEN "Excellent"
ELSEIF [Overall SC Score] >= 80 THEN "Good"
ELSEIF [Overall SC Score] >= 70 THEN "Average"
ELSE "Poor"
END

**Perfect Order Rate (Tableau)**
Recalculated natively in Tableau for use in dashboard filters and cross-sheet comparisons.
([Order Fulfilment Rate %] / 100)
× ([On Time Delivery %] / 100)
× (1 - [Stockout Rate %] / 100)
× 100

**Overall SC Score**
A weighted composite score combining five KPIs into one performance number. Weights reflect business priority — fulfilment and delivery are the most customer-facing, so they carry the most weight.
([Order Fulfilment Rate %] × 0.25)

([On Time Delivery %] × 0.25)
((100 - [Stockout Rate %]) × 0.20)
([Capacity Utilisation %] × 0.15)
([Forecast Accuracy %] × 0.15)


| Component | Weight | Rationale |
|-----------|--------|-----------|
| Order Fulfilment Rate % | 25% | Core customer satisfaction driver |
| On-Time Delivery % | 25% | Direct customer experience impact |
| No Stockout (100 − Stockout%) | 20% | Revenue protection and availability |
| Capacity Utilisation % | 15% | Operational efficiency |
| Forecast Accuracy % | 15% | Planning and demand sensing quality |

---

## 📈 Dashboard Visualizations

### Sheet 1 — KPI Scorecard
**Chart type:** Text Table

This is the executive summary panel — the first thing any stakeholder sees. It presents the six most important headline KPIs averaged across all 100 periods in a single clean row. The purpose is to give an immediate health check without requiring any interaction.

| KPI | Value | vs Benchmark |
|-----|-------|-------------|
| Avg Forecast Accuracy % | 71.9% | Below 85% target ⚠️ |
| Avg On-Time Delivery % | 90.7% | Below 95% target ⚠️ |
| Avg Order Fulfilment % | 92.6% | Below 95% target ⚠️ |
| Avg Service Level % | 95.4% | At target ✅ |
| Total SC Cost | 213,849 | — |
| Avg Overall SC Score | 87.2 | Good range ✅ |

**What this tells us:** Stock availability is the strongest area. Forecasting and delivery are the biggest gaps requiring attention.

---

### Sheet 2 — Forecast Accuracy Trend
**Chart type:** Dual Line Chart

This chart tracks how well the forecasting model performed across all 100 periods. The orange line shows Forecast Accuracy % and the blue line shows Actual Demand. An average reference line anchors the baseline at 71.9%.

**What this tells us:** Forecast accuracy is highly volatile — swinging from near 100% down to -20% in periods 54 and 57 where the forecast was more than double the actual demand. There is no consistent improvement trend across the 100 periods, which suggests the forecasting model is not adapting over time.

**Business action:** Investigate the root cause of the accuracy collapse in periods 50-60. Transition to a Simple Exponential Smoothing model which demonstrated 84.4% accuracy in parallel testing.

---

### Sheet 3 — Perfect Order Rate by Quarter
**Chart type:** Bar Chart with value labels

Groups the Perfect Order Rate by quarter to reveal seasonal or operational patterns. The Perfect Order Rate is the most demanding KPI — all three conditions (fulfilment + on-time + no stockout) must be simultaneously true for an order to qualify as perfect.

| Quarter | Perfect Order Rate |
|---------|--------------------|
| Q1 | 80.0% |
| Q2 | 81.7% ← Best quarter |
| Q3 | 78.5% ← Weakest quarter |
| Q4 | 80.1% |

**What this tells us:** Q2 is the strongest quarter. Q3 consistently underperforms — driven by higher stockout rates and supplier lead time issues in periods 51-75. All four quarters fall below the 85% industry benchmark, indicating systemic issues that no single quarter has solved.

**Business action:** Build pre-Q3 safety stock positions. Review supplier agreements to reduce lead time variability before periods 50-75.

---

### Sheet 4 — Delivery Performance
**Chart type:** Dual Line Chart (On-Time Delivery % vs Service Level %)

Puts two complementary KPIs on the same axis to reveal a critical disconnect. Service Level % (green) tracks stock availability; On-Time Delivery % (blue) tracks logistics execution. When one is high and the other is low, it pinpoints exactly where in the chain the failure is occurring.

**What this tells us:** Service Level stays consistently above 90% — stock is almost always available. But On-Time Delivery drops as low as 80% in some periods. This gap proves the delivery problem is not about inventory — it is about logistics execution. Stock is ready but not reaching customers on time.

**Business action:** Focus on carrier performance audits, route optimization, and warehouse processing times rather than increasing inventory levels.

---

### Sheet 5 — SC Cost Analysis
**Chart type:** Bar Chart colored by Cost Flag

Plots Total SC Cost for every period and color-codes each result by cost band. Blue = High (>3,000), Red = Medium (1,500-3,000), Orange = Low (<1,500). This instantly reveals which periods were cost outliers and whether spikes follow a pattern.

**What this tells us:** Cost ranges from 646 to 4,813 — a nearly 7x difference. High-cost periods are scattered across all four quarters with no seasonal pattern. This suggests cost spikes are driven by unit cost volatility or order volume spikes rather than systemic inefficiency.

**Business action:** Drill into high-cost periods and identify whether Supply_Chain_Cost_per_Unit or Sales_Volume is the primary driver. Negotiate fixed unit cost contracts with suppliers to reduce volatility.

---

### Sheet 6 — Capacity Utilisation vs Total SC Cost
**Chart type:** Bubble Scatter Plot

Each bubble represents one period. X-axis = Capacity Utilisation %, Y-axis = Total SC Cost, bubble size = Overall SC Score, color = Quarter. A trend line reveals whether running at higher capacity drives costs up.

**What this tells us:** There is no strong linear relationship between capacity utilisation and cost. Some of the most expensive periods occur at mid-range capacity (65-75%), not at peak. This means high costs are not simply a function of running the operation hard — cost drivers lie elsewhere.

**Business action:** Cost reduction should target Supply_Chain_Cost_per_Unit through supplier negotiation — not capacity reduction. The operation can safely run at higher utilisation without necessarily increasing costs.

---

### Sheet 7 — Stockout Heatmap
**Chart type:** Square Heatmap by Quarter

A visual grid where each square represents one period and color intensity shows Stockout Rate %. Darker = higher stockout risk. Organized by quarter rows (Q1-Q4) for instant cross-quarter comparison.

**What this tells us:** Stockout rate ranges from 0.14% to 9.96% with an average of 4.63%. Q3 shows the highest concentration of dark squares. Some periods swing from near-zero stockout directly into high stockout — indicating the inventory policy is static and not responding to demand signals dynamically.

**Business action:** Implement a dynamic reorder point model that accounts for Lead Time Variability. The current policy does not adjust for demand volatility or supplier reliability changes.

---

## 🔍 Key Findings

- **Forecast accuracy (71.9%) is the biggest strategic gap** — almost 1 in 3 forecasts is materially wrong, creating a ripple effect across inventory, fulfilment, and cost
- **Delivery failures are a logistics problem, not an inventory problem** — Service Level is 95.4% but OTD is only 90.7%, proving stock is available but not reaching customers on time
- **Q3 is consistently the weakest quarter** — Perfect Order Rate drops to 78.5% driven by stockout spikes in periods 51-75
- **No capacity-cost correlation exists** — high utilisation does not drive high costs, so efficiency gains must come from unit cost reduction not capacity cuts
- **Perfect Order Rate (80.1%) is below the 85% benchmark across all quarters** — systemic improvement is needed simultaneously across fulfilment, delivery, and availability
- **Cost volatility is extreme** — a 7x range from 646 to 4,813 per period suggests reactive rather than planned procurement

---

## 📐 KPI Benchmark Reference

| KPI | Your Average | Industry Target | Status |
|-----|-------------|----------------|--------|
| Forecast Accuracy % | 71.9% | >85% | ⚠️ Below target |
| Perfect Order Rate % | 80.1% | >85% | ⚠️ Below target |
| On-Time Delivery % | 90.7% | >95% | ⚠️ Below target |
| Order Fulfilment % | 92.6% | >95% | ⚠️ Below target |
| Service Level % | 95.4% | >97% | ⚠️ Borderline |
| Inventory Turnover | 4.89x | >5x | ⚠️ Borderline |
| Overall SC Score | 87.2 | >90 | ✅ Good |
| Cash-to-Cash Days | 41.6 days | <40 days | ⚠️ Borderline |

---

## 🛠 Tools Used

- **Microsoft Excel** — Data storage, KPI derivation, formula-based calculated columns
- **Tableau Public** — Interactive dashboard, calculated fields, all visualizations





