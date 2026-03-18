# Online Retail II — Profit Leakage & Customer Segmentation

## Project Overview
This project analyses over 1 million real UK retail transactions from the **UCI Online Retail II dataset** (2009–2011). The goal is to identify profit leakage from anonymous guest shoppers, segment customers by behaviour, and build a machine learning model to predict VIP customers — enabling targeted loyalty conversion campaigns.

---

## Problem Statement
The dataset contains **107,927 transactions with missing Customer IDs** — these are guest shoppers. Most analysts simply drop these records. This project takes a different approach: instead of discarding them, we engineer a business-driven label (`Is_Guest`) and extract actionable insights from this "hidden" segment.

**Key business questions:**
1. Are guest shoppers low-value or high-value customers?
2. Which guests should be targeted for loyalty programme conversion?
3. Which guest behaviour patterns are red flags for supply chain risk?
4. Can we predict which registered customers will become VIPs?

---

## Tools Used
| Tool | Purpose |
|---|---|
| Python | Data cleaning, feature engineering, segmentation |
| Pandas / NumPy | Data manipulation and aggregation |
| Matplotlib / Seaborn | Exploratory visualisation |
| Scikit-learn | Random Forest classifier, AUC-ROC evaluation |

---

## Dataset
- **Source** : [UCI Online Retail II — Kaggle](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci)
- **Size** : ~1 million transactions, 2009–2011
- **Coverage** : UK-based online retailer, international customers

---

## Project Structure
```
online-retail-vip-segmentation/
│
├── notebooks/
│   └── onlineretail.ipynb     ← full analysis notebook
│
└── README.md
```

---

## Methodology

### 1. Data Cleaning & Audit
| Column | Issue | Fix |
|---|---|---|
| Description | 2,928 nulls | Filled with "MANUAL ENTRY" |
| Customer ID | 107,927 nulls | Engineered as Is_Guest = 1 |
| Price | Zero/negative values | Removed (adjustments/gifts) |
| Quantity | Negatives (returns) | Converted to absolute value |

### 2. Feature Engineering
- `Is_Guest` — binary label derived from missing Customer ID
- `hour` — extracted from InvoiceDate for time-of-day analysis
- `Total_Value` — Quantity × Price per transaction
- `Item_Diversity` — unique StockCodes per invoice (measures shopping breadth)
- Order-level aggregation — basket size, average price, total value per invoice

### 3. Guest Segmentation
Each guest order was classified into one of 4 segments:

| Segment | Definition | Count |
|---|---|---|
| Registered User | Has Customer ID | Majority |
| Potential VIP Loyalist | Quantity > 100 AND Item Diversity > 20 | 471 |
| Bulk Guest (Red Flag) | Quantity > 100 AND Item Diversity < 5 | 2 |
| Guest Return (Risk) | Negative Quantity | 219 |
| Standard Guest | All other guests | Remainder |

### 4. Key Business Finding
> **471 anonymous guests contribute ~10% of total revenue despite being only 0.5% of all customers.**

These are the highest priority targets for loyalty programme conversion — they already behave like VIPs but have no registered account.

### 5. VIP Prediction Model
- **Target** : Top 20% spenders among registered users = VIP (1), else Non-VIP (0)
- **Features** : Invoice frequency, StockCode diversity, total quantity
- **Note** : Total_Value was deliberately excluded to prevent data leakage
- **Model** : Random Forest Classifier (100 estimators)
- **Evaluation** : AUC-ROC curve

---

## Key Findings
- Guest shoppers are NOT low-value — average basket of 149 items shows strong purchase intent
- 471 Potential VIP Loyalists identified — top conversion targets for marketing team
- Only 2 Bulk Red Flag guests detected — most guest traffic is genuine
- 219 guest returns are harder to manage operationally due to missing Customer IDs
- Random Forest successfully identifies VIP customers from behavioural signals alone

---

## Business Recommendations
1. **Target Potential VIP Loyalists** for account registration campaigns — offer incentives to convert anonymous high-value guests into registered users
2. **Monitor Bulk Red Flag guests** closely for unusual purchasing patterns that may indicate reseller or supply chain risk
3. **Streamline guest return handling** — build a lightweight guest identification system to reduce friction for the 219 return cases
4. **Use VIP prediction model** to proactively identify registered users approaching VIP threshold and engage them with retention offers before they churn

---

## How to Run
```bash
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl
```
Open `notebooks/onlineretail.ipynb` and update the file path to your local dataset location:
```python
df = pd.read_excel(r"your_path\online_retail_II.xlsx")
```

---

*Dataset source: UCI Machine Learning Repository — Online Retail II*
