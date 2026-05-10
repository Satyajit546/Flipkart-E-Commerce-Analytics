# 🛒 Flipkart E-Commerce Analytics

> **End-to-End Data Analytics Project** | Python · Scikit-learn · Power BI · DAX

A comprehensive analytics pipeline built on simulated Flipkart transactional data. The project spans three independent analysis modules — **Customer Churn & RFM**, **Conversion Funnel**, and **Returns Intelligence** — each delivered as a Python notebook with full visualisations, and summarised in six interactive Power BI dashboards.

---

## 📁 Repository Structure

```
flipkart-ecommerce-analytics/
│
├── notebooks/
│   ├── Flipkart_Churn_analysis.ipynb        # Module 1 — Churn Prediction & RFM
│   ├── Flipkart_Funnel_analysis.ipynb       # Module 2 — Conversion Funnel
│   └── Flipkart_return_rate_analysis.ipynb  # Module 3 — Returns & Quality Score
│
├── dashboards/
│   ├── Sales.jpg              # Sales Executive Summary
│   ├── Customer.jpg           # Customer Analytics
│   ├── Funnel.jpg             # Funnel Analytics
│   ├── Product.jpg            # Product Performance
│   ├── Payment.jpg            # Payment Analytics
│   └── return.jpg             # Return Analytics
│
├── data/
│   └── Flipcart.xlsx          # Source data (6 sheets)
│
└── README.md
```

---

## 📊 Dataset Overview

The source file `Flipcart.xlsx` contains **6 relational sheets**:

| Sheet | Description |
|---|---|
| `customers` | 500 unique customers with state & join date |
| `orders` | 3,000 orders with payment method & order date |
| `order_details` | Line items with product ID, quantity & unit price |
| `products` | 179 products across 5 categories with cost data |
| `events` | Behavioural events: View, AddToCart, Purchase |
| `returns` | 240 return records with reason codes |

**Key business figures:**

| Metric | Value |
|---|---|
| Total Revenue | ₹11,29,39,534 |
| Total Orders | 3,000 |
| Unique Customers | 500 |
| Overall Return Rate | 8% |
| Profit Margin | 28.67% |
| Revenue YoY Growth | 79.55% |

---

## 🔍 Module 1 — Customer Churn & RFM Analysis

**Notebook:** `Flipkart_Churn_analysis.ipynb`

### Objective
Identify customers at risk of churning, segment them into actionable marketing personas using RFM scoring, and build a machine learning model to predict churn probability at the individual level.

### Methodology

**Feature Engineering**
- Computed **Recency, Frequency, Monetary (RFM)** metrics per customer using a snapshot date
- Defined churn as: *no purchase in the last 90 days*
- Extracted behavioural signals from event data: `ViewCount`, `CartCount`, `PurchaseCount`, `CartToViewRatio`
- Mapped top product category and preferred payment method per customer
- Encoded all categoricals with `LabelEncoder` for ML compatibility

**RFM Segmentation — 6 Customer Personas**

| Segment | Definition |
|---|---|
| Champions | Recency < 30d, Frequency ≥ 5, Monetary > ₹50,000 |
| Loyal Customers | Recency < 60d, Frequency ≥ 3 |
| New Customers | Recency < 30d, Frequency ≤ 2 |
| At Risk | Recency 60–120d, Frequency ≥ 3 |
| Lost | Recency ≥ 120d, Frequency ≥ 2 |
| Potential Loyalist | All others |

**Machine Learning Pipeline**

```
Features: Recency, Frequency, Monetary, ViewCount, CartCount,
          PurchaseCount, CartToViewRatio, State_enc,
          TopCategory_enc, PrefPayment_enc

Target: Churned (1 = no purchase in 90 days)

Train/Test Split: 80/20 | Stratified | random_state=42
Scaling: StandardScaler (applied to Logistic Regression only)
```

Three models compared with 5-fold cross-validated ROC-AUC:

| Model | Test AUC | CV-AUC |
|---|---|---|
| Logistic Regression | — | — |
| Random Forest | — | — |
| Gradient Boosting | — | — |

> *Run the notebook to see live scores — results depend on the data split.*

**Output:** Each customer gets a `ChurnProbability` score and a `ChurnRisk` label — `Low Risk`, `Medium Risk`, or `High Risk` — ready for CRM targeting.

### Visualisations (9-panel figure)
- Churn rate by RFM segment
- ROC curves for all three models
- Confusion matrix of the best model
- Feature importance chart
- Churn probability distribution
- Recency vs Monetary scatter by segment
- Customer lifetime value by segment
- Churn rate by state
- Churn risk tier breakdown

---

## 📉 Module 2 — Conversion Funnel & Drop-off Analysis

**Notebook:** `Flipkart_Funnel_analysis.ipynb`

### Objective
Map the end-to-end View → Add to Cart → Purchase funnel, quantify drop-off at each stage, and identify which categories, states, and payment methods convert best and worst.

### Methodology

**Overall Funnel Metrics**

| Stage | Count | Conversion Rate | Drop-off Rate |
|---|---|---|---|
| View | 483 | 100% | — |
| Add to Cart | 480 | 99.4% | 0.6% |
| Purchase | 488 | ~101% | — |

> *Overall Conversion Rate: 1.01 | View-to-Cart Rate: 0.99 | Cart-to-Purchase Rate: 1.02*

**Funnel Slicing**
- **By Category:** Each customer's most-purchased category assigned; View-to-Cart and Cart-to-Purchase rates calculated per category
- **By Payment Method:** Events joined to orders; conversion rate computed for each of the 4 payment methods (Card, COD, NetBanking, UPI)
- **By State:** States labelled as `Top Performer`, `Mid Tier`, or `Needs Attention` based on overall conversion rate

**Time-to-Purchase Analysis**
- Measured days from a customer's first `View` event to their first `OrderDate`
- Useful for setting optimal remarketing and cart-abandonment trigger windows

### Key Findings
- **Haryana** is the consistently top-performing state
- **Bihar** is flagged as `Needs Attention` — lowest conversion
- Overall funnel conversion rate improved significantly from 2023 → 2024 → 2025
- Monthly peaks in View-to-Cart rate occur in **March** (1.08) and **July** (1.07)
- Monthly peaks in Cart-to-Purchase rate occur in **June** (1.28)

---

## 📦 Module 3 — Returns Rate & Product Quality Intelligence

**Notebook:** `Flipkart_return_rate_analysis.ipynb`

### Objective
Diagnose the 8% return rate, attribute it to specific products, categories, states, and return reasons, and build a Product Quality Score that flags high-risk SKUs for supply chain review.

### Methodology

**Master Returns Table**
Merged `returns` → `orders` → `order_details` → `products` → `customers` into a single flat table including `ReturnRevenue = Quantity × UnitPrice`.

**Return Rate by Category**

| Category | Total Orders | Returns | Return Rate |
|---|---|---|---|
| Fashion | — | — | Highest |
| Books | — | — | — |
| Grocery | — | — | — |
| Home | — | — | — |
| Sports | — | — | Lowest |

> *Run the notebook for exact figures.*

**Return Reason Breakdown (overall)**

| Reason | Rank |
|---|---|
| Not as Described | #1 |
| Wrong Product | #2 |
| Size Issue | #3 |
| Damaged | #4 |

**Product Quality Score**

```
QualityScore = 100 − ReturnRate%    (clipped at 0)
HighRisk flag = ReturnRate% ≥ 15%
```

Products with `HighRisk = True` are surfaced for supply chain and catalogue review.

**Statistical Validation**
An independent samples **T-test** compared unit prices of returned vs. non-returned orders to determine whether higher-priced items are disproportionately returned. Result (p-value) is printed in the notebook.

**Return Rate by State**
- **Goa** has the highest return rate (25%)
- **Haryana** has the lowest (5%)
- Strong geographic variation suggests state-level logistics and seller-quality issues

### Visualisations (9-panel figure)
- Return rate by category (bar)
- Return reason heatmap by category
- High-risk product quality scores
- State return rate map / bar
- Revenue lost by category
- Return by payment method (pie)
- Return rate by price band
- Monthly return trend
- T-test result distribution overlay

---

## 📊 Power BI Dashboards

Six interactive reports built in Power BI with slicers for **State**, **Category**, **Payment Method**, **Month**, and **Year**.

### 1. Sales Executive Summary
![Sales Dashboard](dashboards/Sales.jpg)

| KPI | Value |
|---|---|
| Unique Customers | 500 |
| Total Orders | 3,000 |
| Total Revenue | ₹11,29,39,534 |
| Average Order Value | ₹37,646.51 |
| Revenue YoY% | 79.55% |
| Revenue MoM% | 0.19% |
| Profit Margin% | 28.67% |

Top 5 States by Orders: Telangana · Mizoram · Gujarat · Haryana · Andhra Pradesh

---

### 2. Customer Analytics
![Customer Dashboard](dashboards/Customer.jpg)

| KPI | Value |
|---|---|
| Cohort Size | 500 |
| Customer CLV | ₹2,25,879 |
| Avg Purchase Frequency | 6.02 |
| At-Risk Customers | 40 |
| Lost Customers | 25 |
| Need Attention | 49 |

Includes: Customer Joining Rate trend (2020–2025), Top 10 customers by orders, full RFM score table with Customer Segment and Repeat Purchase Rate.

---

### 3. Funnel Analytics
![Funnel Dashboard](dashboards/Funnel.jpg)

| KPI | Value |
|---|---|
| Total Viewers | 483 |
| Total Cart Adds | 480 |
| Total Purchasers | 488 |
| View-to-Cart Rate | 0.99 |
| Cart-to-Purchase Rate | 1.02 |
| Overall Conversion Rate | 1.01 |

Includes: Monthly View-to-Cart and Cart-to-Purchase trend bars, Overall Conversion Rate by year (2023–2025), State performance classification table.

---

### 4. Product Performance
![Product Dashboard](dashboards/Product.jpg)

| KPI | Value |
|---|---|
| Total Products | 179 |
| Total Cost | ₹8,05,59,233 |
| Most Profitable Product | Optio Max |
| Profit Last Month | $32,421,960 |
| Profit per Unit | ₹4,342.85 |
| Top Selling Category | Books |
| Top Best Selling Product | Officiis Pro |
| Profit Margin% | 28.67% |

Includes: Category orders breakdown (donut), Gross profit by products (bar), State order performance, Top 10 best-selling products, Profit by category, Profit zone classification table.

---

### 5. Payment Analytics
![Payment Dashboard](dashboards/Payment.jpg)

| Payment Method | Order Share | Revenue Share |
|---|---|---|
| NetBanking | 26.73% | 26.72% |
| COD | 24.67% | 25.44% |
| Card | 24.40% | 24.44% |
| UPI | 24.20% | 23.40% |

Customer payment demographics: NetBanking (88 customers) > UPI (78) > Card (71) > COD (60)

Includes: Payment breakdown by state (stacked bar across all 28 states).

---

### 6. Return Analytics
![Return Dashboard](dashboards/return.jpg)

| KPI | Value |
|---|---|
| Total Returns | 240 |
| Return Rate% | 8.00% |
| Top Return Reason | Wrong Product |
| Return Rate by Payment | 0.08 |
| Net Revenue (After Returns) | ₹10,36,29,617 |

Return by payment method: Card (29.58%) · COD (27.08%) · UPI (24.58%) · NetBanking (18.75%)

Highest return rate states: Goa (25%) · Jharkhand (18.18%) · Chhattisgarh (14.29%)

---

## 🛠️ Tech Stack

| Layer | Tools |
|---|---|
| Data Wrangling | Python · Pandas · NumPy |
| Machine Learning | Scikit-learn (LogisticRegression, RandomForest, GradientBoosting) |
| Statistical Testing | SciPy (T-test) |
| Visualisation (Python) | Matplotlib · Seaborn |
| BI Dashboards | Microsoft Power BI · DAX |
| Data Source | Microsoft Excel (.xlsx — 6 sheets) |

---

## ⚙️ How to Run

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy openpyxl
```

### Steps
1. Clone this repository
2. Place `Flipcart.xlsx` in the project root (or update the `FILE` path in each notebook)
3. Open any notebook in Jupyter Lab / Jupyter Notebook / VS Code
4. Run all cells top to bottom

```bash
git clone https://github.com/your-username/flipkart-ecommerce-analytics.git
cd flipkart-ecommerce-analytics
jupyter notebook notebooks/Flipkart_Churn_analysis.ipynb
```

---

## 💡 Key Business Insights

- **Churn is predictable** — RFM + behavioural features give strong signal; Gradient Boosting and Random Forest consistently outperform Logistic Regression on this dataset
- **Funnel leakage is minimal** — a 1.01 overall conversion rate suggests the platform is performing well; focus should shift to growing top-of-funnel (Views)
- **8% return rate is concentrated** — "Not as Described" and "Wrong Product" account for the majority of returns; improving product listing quality could cut returns significantly
- **NetBanking dominates** — 26.73% order share and highest customer adoption; COD still strong at 24.67%, suggesting digital payment adoption is ongoing
- **Geographic variance is real** — Telangana leads in orders; Goa and Jharkhand have disproportionately high return rates; state-level interventions are warranted

---

## 📄 License

This project is for educational and portfolio purposes. The dataset is synthetic / simulated.

---

## 🙋 Author

**[Your Name]**
Data Analyst | Python · SQL · Power BI

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?logo=linkedin)](https://linkedin.com/in/your-profile)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?logo=github)](https://github.com/your-username)
