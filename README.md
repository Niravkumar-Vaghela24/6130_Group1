# Enterprise Risk Management Analytics
## Manulife Financial × Mahindra Life Insurance Joint Venture — India Market Entry

**Course: Risk Management Analytics | Signature Assessment**  
**Date: March 2026**

---

## About This Project

This project is a full-cycle enterprise risk management analytics initiative built around a fictitious but realistically grounded business scenario: **Manulife Financial Corporation** entering a 50:50 joint venture with **Mahindra & Mahindra Ltd.** to launch a life insurance company targeting rural and semi-urban India, with a combined capital commitment of USD $400 million.

The project was developed incrementally over the course of the term, with each phase building on the last — from initial threat identification through to quantitative modeling and a final integrated report. Every analytical decision made along the way is documented in the notebooks and reflected in the final report.

The work is grounded in real industry data sources (IRDAI, TRAI, Swiss Re, World Bank) and draws on risk management frameworks from the course text, including Scenario Analytics (Ch. 22), Industry Fusion Analytics (Ch. 17), and Indicators and Warning analysis (Ch. 16).

---

## What We Did — Step by Step

### Phase 1 — SWOT Analysis and Risk Identification

The project started with a structured SWOT analysis of the JV's competitive position in India's life insurance market. From the SWOT, we translated threats and weaknesses into formal risk statements and identified one positive strategic opportunity (regulatory tailwinds) that carries its own risk of underutilization if the JV moves too slowly.

From the SWOT, we built out a full **32-risk universe** across seven categories:

- Competitive
- Financial
- Operational
- Technology / AI
- Information Security
- Legal / Compliance
- Reputational / Ethical

Each risk was described using a structured cause-consequence format: *"The risk of [X], caused by [Y], resulting in [Z]."* This format forces specificity and makes the risk measurable rather than vague.

### Phase 2 — Risk Register and Scoring

All 32 risks were entered into a formal Risk Treatment and Response Plan (RT&RP), scored on a calibrated 1–9 likelihood and impact scale designed specifically for the JV's operating context. The impact scale was broken into six dimensions — Financial, Operational, Competitive, Reputational, Technical, and Legal/Compliance — and anchored in JV-specific dollar thresholds (e.g., "Somewhat High Impact = USD $100–250M financial loss") rather than generic language.

Each risk was scored on both dimensions, a composite risk score was calculated (Likelihood × Impact), and risks were classified as High (≥45), Medium (20–44), or Low (<20).

A **risk heat map** was built to visualize the 32 risks across the likelihood/impact matrix, making the priority distribution immediately visible to decision makers.

### Phase 3 — Exploratory Data Analysis (`eda.ipynb`)

Before any modeling, we conducted a full EDA pass across all five datasets to understand distributions, confirm data quality, and extract parameters that would feed directly into the Monte Carlo simulations. Every finding was tied back to a specific risk or model input — this was not a generic EDA pass.

**Risk Register EDA:** Score distribution histogram, severity pie chart, and mean risk score by category. Confirmed 9 HIGH risks and established the category breakdown used throughout the project.

**IRDAI Approval History (`irdai_approval_history.csv`):** We fitted a Log-Normal distribution to the 23 historical approval timelines for foreign-domestic JV applications from 2015–2024. The fitted parameters (mean ~13.8 months, P90 ~21 months) were carried directly into the R9 Monte Carlo simulation — no hardcoded guesses.

**India Insurance Market (`india_insurance_market.csv`):** Market share trend analysis from 2019–2024 for LIC, HDFC Life, ICICI Prudential, and SBI Life. LIC's declining share trend (74.8% → 60.8%) was extracted as the competitor growth rate input for R1's simulation.

**AI Fairness Applicants (`ai_fairness_applicants.csv`):** Demographic parity analysis on 500 applicant records. We computed the actual rural-to-urban AI approval rate ratio, confirmed it falls below the IRDAI 0.80 fairness threshold, and used this observed parity value to fit the Beta distribution parameters for the R15 Monte Carlo.

**LIC Competitor Data (`lic_competitor_data.csv`):** 36 months of agent count and customer acquisition cost (CAC) data across LIC, HDFC Life, ICICI Prudential, and SBI Life. Mean CAC (₹1,800) and its standard deviation were extracted to parametrize the LogNormal CAC distribution in R1's simulation.

### Phase 4 — Qualitative Risk Assessment (`qualitative_analysis.ipynb`)

A **Random Forest Classifier** (100 trees) was trained on all 32 risks using Likelihood Score and Impact Score as features, using **5-fold cross-validation** throughout — the dataset is too small for a single train/test split. The notebook produced a classification report, confusion matrix heatmap, and an annotated risk severity scatter plot.

The key purpose was not just to classify risks, but to verify that the qualitative severity thresholds are internally consistent — that the rule-based labels the team assigned match what a data-driven model would independently predict. The three focus risks — R1 (Competitive Displacement), R9 (IRDAI Regulatory Delay), and R15 (AI Underwriting Bias) — were confirmed as correctly classified and selected for quantitative deep-dive based on their shared maximum risk score of 56.

### Phase 5 — Quantitative Risk Assessment (`quantitative_analysis.ipynb`)

This was the analytical core of the project, combining **Monte Carlo simulation**, **ML-based severity prediction**, and **three-dimensional risk prioritization**.

**Monte Carlo Simulation — R1: LIC & Competitor Displacement:**  
Parameters were pulled directly from EDA outputs (LIC market share change rates from `india_insurance_market.csv`, CAC from `lic_competitor_data.csv`). The simulation modeled JV Year-1 market share as a function of four stochastic variables: competitor growth (Normal), agent ramp-up speed (Triangular), pricing premium (Normal), and CAC (LogNormal). The viability threshold was set at 1.5% market share — the minimum for the JV to cover Year-1 operating costs.

**Monte Carlo Simulation — R9: IRDAI Regulatory Delay:**  
Log-Normal parameters fitted from `irdai_approval_history.csv` in EDA. The simulation produced approval timeline and total financial exposure distributions. A warning threshold of 18 months was applied based on IRDAI's escalation rules for stalled applications.

**Monte Carlo Simulation — R15: AI Underwriting Bias:**  
Beta distribution parameters derived from the observed demographic parity score in `ai_fairness_applicants.csv` using the method of moments. Financial impact (legal costs + IRDAI fine + reputational loss) was only triggered in simulation iterations where the parity score fell below the IRDAI 0.80 threshold.

**Three-Dimensional Risk Prioritization (Jalilvand & Moorthy, 2023, JRFM 16:473):**  
Extended the standard 2D risk score by adding Control Level using the RIMS Risk Maturity Model (RMM) 5-point scale. All 32 risks were assigned a control level, and a Control-Adjusted (CA) Index was computed as Expected Impact ÷ Control Level. Risks that move significantly between their EI rank and CA rank are those where control quality is materially affecting urgency. Two heat maps were produced: a standard 2D heat map and a 3D version where each dot's border color encodes its RIMS control level.

### Phase 6 — Data-Driven Monte Carlo (`monte_carlo.ipynb`)

This notebook re-runs the three Monte Carlo simulations with one key difference from `quantitative_analysis.ipynb`: all distribution parameters are fitted live from the raw CSV files at runtime, rather than being carried in from EDA. This makes the notebook a fully self-contained, reproducible simulation pipeline — changing the underlying data files automatically updates all simulation outputs without any manual parameter editing. Each risk section begins by printing the fitted parameters alongside the raw data statistics they were derived from, so a reader can verify the link between data and model input at a glance.

### Phase 7 — Integrated Report (`report/final_report.docx`)

All prior work was synthesized into a structured 7+ page Signature Assessment report delivered to a notional Board Risk Committee audience. The report integrates the qualitative and quantitative work into a coherent narrative with figures, tables, and management-ready recommendations covering Background, Risk Identification, Qualitative Assessment, Quantitative Assessment, Risk Response Strategy, KRIs and trigger points, and Conclusion.

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

## Analytical Methods Used

| Method | Applied To | Notebook | Data Source |
|--------|------------|----------|-------------|
| Risk Scoring (1–9 calibrated scale) | All 32 risks | — | `risk_register_data.xlsx` |
| Standard Risk Heat Map (2D) | All 32 risks | `quantitative_analysis.ipynb` | `risk_register_data.xlsx` |
| Three-Dimensional Heat Map (RIMS RMM) | All 32 risks | `quantitative_analysis.ipynb` | `risk_register_data.xlsx` |
| Log-Normal Distribution Fitting | IRDAI approval timelines | `eda.ipynb` | `irdai_approval_history.csv` |
| Market Share Trend Analysis | LIC / competitor share | `eda.ipynb` | `india_insurance_market.csv` |
| Demographic Parity Analysis | AI fairness gap | `eda.ipynb` | `ai_fairness_applicants.csv` |
| CAC Distribution Fitting | Agent cost inputs | `eda.ipynb` | `lic_competitor_data.csv` |
| Random Forest Classifier (severity prediction) | All 32 risks | `qualitative_analysis.ipynb` | `risk_register_data.xlsx` |
| 5-Fold Cross-Validation | RF model evaluation | `qualitative_analysis.ipynb` | `risk_register_data.xlsx` |
| Monte Carlo — R1 (Competitor Displacement) | Market share viability | `quantitative_analysis.ipynb`, `monte_carlo.ipynb` | `india_insurance_market.csv`, `lic_competitor_data.csv` |
| Monte Carlo — R9 (Regulatory Delay) | Approval timeline & cost | `quantitative_analysis.ipynb`, `monte_carlo.ipynb` | `irdai_approval_history.csv` |
| Monte Carlo — R15 (AI Bias) | Parity score & financial impact | `quantitative_analysis.ipynb`, `monte_carlo.ipynb` | `ai_fairness_applicants.csv` |
| 3D Risk Prioritization (Jalilvand & Moorthy, 2023) | All 32 risks | `quantitative_analysis.ipynb` | `risk_register_data.xlsx` |

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

## Author

Enterprise Risk Management Analytics — Course Signature Assessment, March 2026
