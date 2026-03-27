# Enterprise Risk Management Analytics
## Manulife Financial × Mahindra Life Insurance Joint Venture — India Market Entry

**Course: Risk Management Analytics | Signature Assessment**  


---

## About This Project 

This project is a full-cycle enterprise risk management analytics initiative built around a fictitious but realistically grounded business scenario: **Manulife Financial Corporation** entering a 50:50 joint venture with **Mahindra & Mahindra Ltd.** to launch a life insurance company targeting rural and semi-urban India, with a combined capital commitment of USD $400 million.

The project was developed incrementally over the course of the term, with each phase building on the last — from initial threat identification through to quantitative modeling and a final integrated report. Every analytical decision made along the way is documented in the notebooks and reflected in the final report.

The work is grounded in real industry data sources (IRDAI, TRAI, Swiss Re, World Bank) and draws on risk management frameworks from the course text, including Scenario Analytics (Ch. 22), Industry Fusion Analytics (Ch. 17), and Indicators and Warning analysis (Ch. 16).

---





## What We Did — Step by Step

### Phase 1 — SWOT Analysis and Risk Identification

We started with a SWOT analysis of the JV's competitive position in India's life insurance market, translating threats and weaknesses into formal risk statements using a cause-consequence format: *"The risk of [X], caused by [Y], resulting in [Z]."* This produced a **32-risk universe** across seven categories: Competitive, Financial, Operational, Technology/AI, Information Security, Legal/Compliance, and Reputational.

### Phase 2 — Risk Register and Scoring

All 32 risks were entered into a Risk Treatment and Response Plan (RT&RP) and scored on a calibrated 1–9 likelihood and impact scale anchored in JV-specific dollar thresholds. Composite scores (Likelihood × Impact) classified risks as High (≥45), Medium (20–44), or Low (<20), and a risk heat map was built to make the priority distribution immediately visible.

### Phase 3 — Exploratory Data Analysis (`eda.ipynb`)

Before any modeling, we ran a full EDA across all five datasets — every finding was tied to a specific risk input, not just general exploration.

- **Risk Register:** Confirmed 9 HIGH risks and established the category breakdown used throughout.
- **`irdai_approval_history.csv`:** Fitted a Log-Normal distribution to 23 historical JV approval timelines (mean 13.8 months, P90 ~21 months) — carried directly into the R9 simulation.
- **`india_insurance_market.csv`:** Extracted LIC's year-on-year market share decline (74.8% → 60.8%) as the competitor growth rate input for R1.
- **`ai_fairness_applicants.csv`:** Computed the rural-to-urban AI approval parity score (0.711), confirmed it falls below IRDAI's 0.80 threshold, and used it to fit Beta distribution parameters for R15.
- **`lic_competitor_data.csv`:** Extracted mean CAC (₹1,775) and standard deviation from 36 months of competitor data to parametrize the LogNormal CAC distribution in R1.

### Phase 4 — Qualitative Risk Assessment (`qualitative_analysis.ipynb`)

A Random Forest Classifier (100 trees, 5-fold cross-validation) was trained on all 32 risks to verify that our rule-based severity labels are internally consistent — confirming the three focus risks R1, R9, and R15 were correctly classified at their shared maximum score of 56.

### Phase 5 — Quantitative Risk Assessment (`quantitative_analysis.ipynb`)

The analytical core of the project — combining Monte Carlo simulation, ML-based severity prediction, and three-dimensional risk prioritization.

- **R1 (Competitor Displacement):** Modeled JV Year-1 market share using four stochastic inputs fitted from real data (competitor growth, agent ramp-up, pricing premium, CAC). Viability threshold set at 1.5% market share.
- **R9 (Regulatory Delay):** Log-Normal approval timeline distribution fitted from IRDAI history; 18-month warning threshold applied.
- **R15 (AI Bias):** Beta distribution fitted from observed parity data; financial impact triggered only when parity score falls below 0.80.
- **3D Risk Prioritization (Jalilvand & Moorthy, 2023):** Extended the 2D score with RIMS RMM control levels to produce a Control-Adjusted Index, generating both standard and control-overlay heat maps for all 32 risks.

### Phase 6 — Data-Driven Monte Carlo (`monte_carlo.ipynb`)

A self-contained version of the three simulations where all distribution parameters are fitted live from the raw CSVs at runtime. Changing the underlying data automatically updates all outputs — no manual parameter editing required.

### Phase 7 — Integrated Report (`report/final_report.pdf`)

All prior work synthesized into a 7+ page Signature Assessment report covering Background, Risk Identification, Qualitative Assessment, Quantitative Assessment, Risk Response Strategy, KRIs, and Conclusion — with 8 figures embedded directly from the notebook outputs.

---


## Repository Structure

```
enterprise-risk-project/
│
├── README.md                          ← You are here
├── requirements.txt                   ← Python dependencies
│
├── data/
│   ├── raw/
│   │   ├── risk_register_data.xlsx          # 32-risk scored register (source data)
│   │   ├── Manulife_Mahindra_Risk_Register.xlsx  # Full RT&RP workbook (5 sheets)
│   │   ├── irdai_approval_history.csv       # 23 IRDAI JV approval records (2015–2024)
│   │   ├── india_insurance_market.csv       # Market share & size data (2019–2024)
│   │   ├── ai_fairness_applicants.csv       # 500 applicant records for AI bias analysis
│   │   └── lic_competitor_data.csv          # 36 months agent count & CAC data
│   └── processed/
│       ├── risk_heatmap.png                 # 32-risk heat map visualization
│       ├── scenario_analytics.png           # Scenario analytics chart
│       ├── probability_distributions.png    # Beta distributions
│       ├── ml_feature_importance.png        # RF feature importance
│       └── monte_carlo_results.png          # Monte Carlo simulation output
│
├── notebooks/
│   ├── eda.ipynb                      # EDA across all 5 datasets; distribution fitting; parameter extraction
│   ├── qualitative_analysis.ipynb     # Random Forest classifier; severity validation; confusion matrix
│   ├── quantitative_analysis.ipynb    # Data-driven Monte Carlo (R1, R9, R15); 3D risk prioritization; dual heat maps
│   └── monte_carlo.ipynb              # Self-contained data-driven Monte Carlo; live parameter fitting from CSVs
│
└── report/
    └── final_report.docx              # Signature Assessment Report (7+ pages)
```

---



## How to Run the Notebooks

```bash
# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook

# Run notebooks in this order — each depends on the prior step:
# 1. eda.ipynb                   — EDA + distribution fitting across all datasets
# 2. qualitative_analysis.ipynb  — RF classifier + severity validation
# 3. quantitative_analysis.ipynb — Monte Carlo simulations + 3D risk analysis
# 4. monte_carlo.ipynb           — Self-contained data-driven Monte Carlo (standalone)

# All notebooks load data from: /content/ (Google Colab) or update paths to local data/raw/
```

> **Google Colab users:** Upload all files from `data/raw/` to `/content/` before running.

---

## Data Sources

| File | Description | Used In |
|------|-------------|---------|
| `risk_register_data.xlsx` | 32 risks with Likelihood, Impact, Risk Score | All notebooks |
| `Manulife_Mahindra_Risk_Register.xlsx` | Full RT&RP workbook with KRI framework, heat map, scoring sheet | Report |
| `irdai_approval_history.csv` | 23 IRDAI JV approval records (2015–2024): timelines, queries, holding costs | `eda.ipynb`, `quantitative_analysis.ipynb`, `monte_carlo.ipynb` |
| `india_insurance_market.csv` | Annual market share (LIC, HDFC, ICICI, SBI) and market size 2019–2024 | `eda.ipynb`, `quantitative_analysis.ipynb`, `monte_carlo.ipynb` |
| `ai_fairness_applicants.csv` | 500 applicants (Urban/Rural) with AI approval outcomes and demographic features | `eda.ipynb`, `quantitative_analysis.ipynb`, `monte_carlo.ipynb` |
| `lic_competitor_data.csv` | 36 months of agent counts and CAC across LIC and private insurers | `eda.ipynb`, `quantitative_analysis.ipynb`, `monte_carlo.ipynb` |

---

## Key Assumptions and Limitations

- **Monte Carlo parameter fitting:** Distribution parameters (Log-Normal for IRDAI timelines, Beta for parity scores, LogNormal for CAC) are fitted from the provided datasets using standard moment-matching. The datasets represent the best available proxies — results should be recalibrated when live JV operational data becomes available after Month 3.
- **Viability threshold (R1):** The 1.5% Year-1 market share threshold was set based on JV cost structure estimates. Sensitivity to this threshold can be assessed by modifying the `VIABILITY` variable in `quantitative_analysis.ipynb`.
- **3D control levels:** RIMS RMM control levels were assigned judgmentally based on the maturity of corresponding controls in comparable insurance JV launches. They are intended to indicate relative control strength, not absolute ratings.
- **Demographic parity variance:** The method-of-moments estimate for Beta distribution variance uses an industry estimate of 0.02 for parity score spread. This can be updated if a larger applicant dataset becomes available.

---
