# Quantifying Clinical Gaps in Valvular Heart Disease: Using the Medication Burden Index (MBI) to Triage High-Complexity Intervention Candidates.
## 📄 Executive summary

| Section | Content |
|:---|:---|
|**Context**|International Cardiology mission phase 1 (2025 cohort) analysis of Valvular Heart Disease in León, Nicaragua|
| **Challenge** | In this 10-day high-volume "clinical sprint," cardiologists often skip fields for healthy valves. If a valve is normal, the entry is left blank to save time. This creates Missing Not At Random (MNAR) bias. |
| **Strategy** | Natural Normal Imputation: Gaussian noise centered around healthy means (e.g., RVSP $25 \pm 4 \text{ mmHg}$) to restore the statistical variance of a healthy population. |
| **Key metric** | Medication Burden Index (MBI): A quantitative surrogate for clinical complexity and pulmonary hypertension based on pharmacological intensity. |
| **Outcome** | MBI-based triage proposal for identification of complex and high risk patients. |

## 📊 Outcome: MBI-driven triage
The MBI score serves as a robust proxy for anatomical complexity and critical hemodynamic compromise (AUC: 0.82).

![MBI Zones](assets/output_triage_zones.png)

Based on the cohort analysis, these thresholds guide the intake staff in prioritizing patients.

| MBI Range | Triage Zone | Clinical Interpretation |
|:---|:---|:---|
| **< 4.0** | **Zone 1: Standard** | Likely compensated. |
| **4.0 - 5.25** | **Zone 2: Complex** | Optimal intervention area: High probability of hemodynamic complexity. |
| **5.25 - 5.5** | **Zone 3: Critical** | **85% Precision** for Critical Pulmonary Hypertension ($RVSP > 60~mmHg$). |
| **> 5.5** | Zone 4: Futility risk | intervention risk likely outweighs potential hemodynamic gain. |

## 💻 Technical structure
### Project Schema
```text
├── data/
│   ├── raw/                                    # Original CSV
│   └── processed/                              # Post-Transformation data
├── notebooks/
│   ├── 01_extraction_transformation.ipynb      # Extraction & Transformation
│   ├── 02_loading.ipynb                        # MySQL connector script
│   └── 03_analysis.ipynb                       # Data Analysis
├── sql/                                        # .sql scripts for database queries
├── tableau/                                    # Tableau files
├── requirements.txt                            # Python library dependencies
└── README.md
```

[//]: # (Database schema)

## 📈 Data integrity and analysis
### The Medication Burden Index (MBI)
The MBI transforms fragmented medication lists into a single, actionable score that quantifies how aggresively a patient is being medically managed, which serves as a proxy for both physiological severity and care complexity.

We define the score mathematically as:

$$MBI = \sum \left( \text{Class Weight} \times \frac{\text{Total Daily Dose}}{\text{Maximum Daily Dose}} \right)$$

* **Dose Ratio:** The total milligrams consumed by the patient in 24 hours. For example, a patient on Furosemide 40mg every 12h has a $TDD=80~mg$

* **Weight:** Based on the drug's therapeutic impact for valvular heart disease (e.g., Loop Diuretics have a higher weight than Lipid Lowering agents).

| Weight | Priority | Medication Classes |
|:---|:---|:---|
| **3.0** | Critical | Loop diuretics, pulmonary vasodilators. |
| **2.0** | High | RAAS inhibitors, beta-blockers, SGLT2 inhibitors. |
| **1.0** | Moderate | Anticoagulants, calcium channel blockers. |
| **0.5** | Maintenance | Statins. |

**Implementation example: Normalizing calcium channel blockers (CCBs)**

To ensure the MBI reflects true clinical intensity rather than raw milligrams, we normalize dosages against their therapeutic ceilings. Consider two patients on Calcium Channel Blockers (CCBs):

| Patient | Medication | Raw dosage | Frequency | Total Daily Dose (TDD) | Maximum Daily Dose (MDD) | Dose ratio |
| :- | :- | -: | :- | -: | -: | -: |
| A | **Nifedipine** | 20 mg | BID | 40 mg | 120 mg | 0.33 |
| B | **Amlodipine** | 10 mg | QD | 10 mg | 10 mg | 1.00 |

**Clinical Insight:** Despite Patient A taking a higher milligram count (40mg vs 10mg), Patient B is at a higher therapeutic intensity (100% of max dose). The MBI correctly captures this higher therapeutic intensity for Patient B, which would be lost in a non-normalized dataset.

[//]: # (Imputation to restore physiologic variance)

[//]: # (MBI ROC for high RVSP)

[//]: # (Regression with outliers)

[//]: # (Tableau: Visualization)