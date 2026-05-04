# 🚚 Supply Chain Performance Dashboard

## 📌 Purpose
This dashboard was built to demonstrate how supply chain data can be transformed into actionable business intelligence. The goal was to go beyond simply visualizing numbers — and instead build a decision-driving tool that helps operations, planning, and leadership teams identify problems, understand root causes, and take targeted action.

The dataset contains **17 supply chain KPIs** across **100 periods** covering demand forecasting, inventory management, order fulfillment, delivery performance, supplier lead times, and cost efficiency.

---

## 🧮 Calculated Fields Created
All calculated fields were built from scratch to derive deeper insights:

**1. Forecast Accuracy %**
```tableau
(1 - ABS([Actual_Demand] - [Forecast_Demand]) / [Actual_Demand])
```
Measures how close predictions were to actual demand

**2. Forecast Error**
```tableau
[Actual_Demand] - [Forecast_Demand]
```
Positive = under forecast, Negative = over forecast

**3. Inventory Health Score**
```tableau
IF [Inventory_Turnover] >= 6 AND [Stockout_Rate_%] < 5 THEN "Healthy"
ELSEIF [Inventory_Turnover] >= 4 AND [Stockout_Rate_%] < 8 THEN "Moderate"
ELSE "At Risk"
END
```
Combines two metrics to classify inventory health

**4. Service Level Score**
```tableau
([Order_Fulfilment_Rate_%] + [On_Time_Delivery_%]) / 2
```
Single composite customer service metric

**5. Stockout Risk Flag**
```tableau
IF [Stockout_Rate_%] > 7 THEN "HIGH RISK"
ELSEIF [Stockout_Rate_%] > 4 THEN "MODERATE"
ELSE "LOW RISK"
END
```

---

## 🔍 Key Insights Discovered

### 1. Forecast Accuracy is the Biggest Gap
- Average forecast accuracy of **71.9%** across 100 periods
- Significant drops in periods 53-55 indicating potential disruptions
- Improving forecast accuracy is the single biggest lever for performance improvement

### 2. Inventory Health is Critical
- Only **14 out of 100 periods** were classified as Healthy
- **42 periods At Risk** and **44 periods Moderate**
- 86% of periods showed suboptimal inventory health

### 3. Stockouts are NOT just a Forecasting Problem
- Scatter plot shows stockouts occurring across ALL forecast error ranges
- Supplier lead time variability and capacity constraints also drive stockouts
- Multi-dimensional problem requiring multi-dimensional solution

### 4. On Time Delivery Never Consistently Hits 95% Target
- High volatility between 80-100% with no improvement trend
- Suggests reactive firefighting rather than systematic process management

### 5. Supplier Lead Time Impact is Inconclusive
- Dots spread across all lead time ranges (5-30 days)
- Both high and low delivery performance at every lead time level
- Internal operational factors may be more impactful than supplier performance

---

## 🛠️ Skills Demonstrated

| Skill | Details |
|-------|---------|
| **Tableau** | Dashboard design, calculated fields, dual axis charts, scatter plots, reference lines, trend lines |
| **Supply Chain Analytics** | KPI design, inventory management, demand planning, service level analysis |
| **Data Storytelling** | Translating raw data into actionable business decisions |
| **Critical Thinking** | Challenging assumptions with data evidence |

---

## 📊 Dashboard Preview

https://public.tableau.com/app/profile/ahsan.riaz6023/viz/SupplyChainPerformanceDashboard_17778651547260/Dashboard2
