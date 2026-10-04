# Bank Marketing Project — Findings Log
*Running notes to build the final report from. Update after each stage.*

---

## Business Understanding
- Business problem: predict which customers are likely to subscribe to a term deposit, to help the bank target marketing calls more effectively and reduce wasted contact attempts
- Dataset: UCI Bank Marketing (bank-full.csv), 45,211 records, 17 original attributes

## Data Understanding
- Class imbalance confirmed: y = no (39,922, 88.3%) vs yes (5,289, 11.7%)
- `balance`: min -8,019, max 102,127, avg 1,362 — heavily right-skewed with extreme outliers
- `education` has 1,857 "unknown" (~4%); `job` has 288 "unknown" (~0.6%) — kept as own category, not imputed
- `pdays = -1` means "never previously contacted" — not a true numeric magnitude (addressed in Phase 2 feature engineering)
- Fig 1: Count by job, split by y — blue-collar/management have most customers but modest conversion
- Fig 2: Average balance by job, split by y — subscribers have higher avg balance than non-subscribers across nearly every job category (exception: unemployed, where it reverses) — suggests balance acts as a proxy signal, not necessarily a direct driver once other factors are controlled

## Phase 1 — Baseline Models (single 70/30 split)
- Rationale: establish baseline with minimal preprocessing before investing in feature engineering, to measure whether later improvements actually help
- Decision Tree (imbalanced baseline): 88.99% accuracy, but only 20.42% yes-recall (98.07% no-recall) — classic imbalance artifact, model just predicts majority class
- Applied 50/50 class balancing via Sample operator
- Decision Tree (balanced): 77.22% accuracy, 68.68% yes-recall, 85.76% no-recall — accuracy dropped but model became far more useful
- Decision Tree structure: top splits were pdays, duration, age, balance (job did not appear)
- Naive Bayes (balanced): 78.23% accuracy, 76.81% yes-recall, 79.65% no-recall, 79.05% yes-precision — outperformed Decision Tree on this split

## Phase 2 — Refined Pipeline
- Feature engineering: added `previously_contacted` (binary, derived from pdays == -1)
- Information Gain ranking (top to bottom): duration (0.065), poutcome (0.042), month (0.035), contact (0.020), pdays (0.018), previous/previously_contacted (0.017 each), housing (0.014), age/job (0.012), balance (0.006), education/loan/campaign (0.004), marital (0.003), day (0.001), default (0.000)
- Key finding: duration dominates Information Gain by a wide margin — confirms data leakage concern (duration unknown before a call happens)
- Key finding: previously_contacted scored nearly identical to raw pdays/previous — engineered feature didn't dramatically outperform raw data as hypothesized (honest negative result)
- Key finding: balance ranked low in Information Gain (0.006) despite looking important in exploratory visualisation (Fig 2) — visual group averages don't always translate to strong individual-record predictive power
- Rebuilt both models inside 10-fold Cross Validation (more robust than single split)
- Decision Tree (CV): 71.75% ± 2.30% accuracy, 93.67% yes-recall, 49.84% no-recall, 65.12% yes-precision
- Naive Bayes (CV): 76.36% ± 1.23% accuracy, 73.13% yes-recall, 79.58% no-recall, 78.17% yes-precision

## Cross-Phase Comparison Table
| Model | Accuracy | Yes Recall | No Recall |
|---|---|---|---|
| DT baseline (imbalanced) | 88.99% | 20.42% | 98.07% |
| DT balanced (single split) | 77.22% | 68.68% | 85.76% |
| NB balanced (single split) | 78.23% | 76.81% | 79.65% |
| DT balanced (10-fold CV) | 71.75% ± 2.30% | 93.67% | 49.84% |
| NB balanced (10-fold CV) | 76.36% ± 1.23% | 73.13% | 79.58% |

## Overall Conclusions (draft)
- Accuracy alone is a misleading metric for this dataset due to severe class imbalance — always paired with recall/precision
- Naive Bayes was the more stable, balanced performer across both phases (higher accuracy, lower variance, more even recall)
- Decision Tree (CV) pushed furthest toward catching subscribers (93.67% yes-recall) but at a steep cost to reliability (49.84% no-recall, highest variance)
- Model choice is a genuine business trade-off: DT-CV if missing a subscriber is very costly; NB if a balanced, reliable, generally-applicable model is preferred
- Call-behaviour/campaign features (duration, poutcome, month, contact, pdays) dominate predictive power; customer demographic/financial features (age, job, marital, education, balance) are consistently weak — subscription outcomes appear driven more by campaign execution than by who the customer is

## Still to do
- [ ] Sensitivity test: Information Gain / models without `duration` (realistic pre-call-only scenario)
- [ ] Finalise figure numbering and captions
- [ ] Write up full report in CRISP-DM structure
- [ ] Repeat for group's 2nd dataset (Credit Default)
# Bank Marketing Project — Findings Log
*Running notes to build the final report from. Update after each stage.*

---

## Business Understanding
- Business problem: predict which customers are likely to subscribe to a term deposit, to help the bank target marketing calls more effectively and reduce wasted contact attempts
- Dataset: UCI Bank Marketing (bank-full.csv), 45,211 records, 17 original attributes

## Data Understanding
- Class imbalance confirmed: y = no (39,922, 88.3%) vs yes (5,289, 11.7%)
- `balance`: min -8,019, max 102,127, avg 1,362 — heavily right-skewed with extreme outliers
- `education` has 1,857 "unknown" (~4%); `job` has 288 "unknown" (~0.6%) — kept as own category, not imputed
- `pdays = -1` means "never previously contacted" — not a true numeric magnitude (addressed in Phase 2 feature engineering)
- Fig 1: Count by job, split by y — blue-collar/management have most customers but modest conversion
- Fig 2: Average balance by job, split by y — subscribers have higher avg balance than non-subscribers across nearly every job category (exception: unemployed, where it reverses) — suggests balance acts as a proxy signal, not necessarily a direct driver once other factors are controlled

## Phase 1 — Baseline Models (single 70/30 split)
- Rationale: establish baseline with minimal preprocessing before investing in feature engineering, to measure whether later improvements actually help
- Decision Tree (imbalanced baseline): 88.99% accuracy, but only 20.42% yes-recall (98.07% no-recall) — classic imbalance artifact, model just predicts majority class
- Applied 50/50 class balancing via Sample operator
- Decision Tree (balanced): 77.22% accuracy, 68.68% yes-recall, 85.76% no-recall — accuracy dropped but model became far more useful
- Decision Tree structure: top splits were pdays, duration, age, balance (job did not appear)
- Naive Bayes (balanced): 78.23% accuracy, 76.81% yes-recall, 79.65% no-recall, 79.05% yes-precision — outperformed Decision Tree on this split

## Phase 2 — Refined Pipeline
- Feature engineering: added `previously_contacted` (binary, derived from pdays == -1)
- Information Gain ranking (top to bottom): duration (0.065), poutcome (0.042), month (0.035), contact (0.020), pdays (0.018), previous/previously_contacted (0.017 each), housing (0.014), age/job (0.012), balance (0.006), education/loan/campaign (0.004), marital (0.003), day (0.001), default (0.000)
- Key finding: duration dominates Information Gain by a wide margin — confirms data leakage concern (duration unknown before a call happens)
- Key finding: previously_contacted scored nearly identical to raw pdays/previous — engineered feature didn't dramatically outperform raw data as hypothesized (honest negative result)
- Key finding: balance ranked low in Information Gain (0.006) despite looking important in exploratory visualisation (Fig 2) — visual group averages don't always translate to strong individual-record predictive power
- Rebuilt both models inside 10-fold Cross Validation (more robust than single split)
- Decision Tree (CV): 71.75% ± 2.30% accuracy, 93.67% yes-recall, 49.84% no-recall, 65.12% yes-precision
- Naive Bayes (CV): 76.36% ± 1.23% accuracy, 73.13% yes-recall, 79.58% no-recall, 78.17% yes-precision

## Cross-Phase Comparison Table
| Model | Accuracy | Yes Recall | No Recall |
|---|---|---|---|
| DT baseline (imbalanced) | 88.99% | 20.42% | 98.07% |
| DT balanced (single split) | 77.22% | 68.68% | 85.76% |
| NB balanced (single split) | 78.23% | 76.81% | 79.65% |
| DT balanced (10-fold CV, all features) | 71.75% ± 2.30% | 93.67% | 49.84% |
| NB balanced (10-fold CV, all features) | 76.36% ± 1.23% | 73.13% | 79.58% |
| NB (10-fold CV, top-8 IG features) | 74.18% ± 1.60% | 66.84% | 81.53% |
| **DT (10-fold CV, top-8 IG features)** | **79.11% ± 2.03%** | 80.96% | 77.25% |

## Sensitivity Test — Duration Removed (Naive Bayes, CV)
- Rationale: duration dominates Information Gain (0.065) but is unknown before a call happens, so it can't be used for real pre-call targeting — tested how much performance relies on this leaky feature
- With duration: 76.36% ± 1.23% accuracy, 73.13% yes-recall, 79.58% no-recall, 78.17% yes-precision
- Without duration: 67.42% ± 1.64% accuracy, 59.03% yes-recall, 75.82% no-recall, 70.94% yes-precision
- Key finding: ~9-point accuracy drop and ~14-point yes-recall drop when duration is excluded — quantifies the gap between headline model performance and genuine real-world deployable performance. The "honest" ceiling for a pre-call targeting model is ~67.42% accuracy, not 76.36%.

## Feature Selection Test — Top 8 Information Gain Features (Naive Bayes, CV, duration included)
- Rationale: test whether trimming low-ranked features (age, job, balance, education, loan, campaign, marital, day, default) improves performance by removing noise
- Kept features: duration, poutcome, month, contact, pdays, previous, previously_contracted, housing (+ y)
- Full 18 features: 76.36% ± 1.23% accuracy, 73.13% yes-recall, 79.58% no-recall, 78.17% yes-precision
- Top 8 features: 74.18% ± 1.60% accuracy, 66.84% yes-recall, 81.53% no-recall, 78.35% yes-precision
- Key finding: trimming features slightly HURT accuracy (-2.18pts) and yes-recall (-6.29pts), despite those features individually scoring low on Information Gain. Yes-precision stayed flat and no-recall improved slightly.
- Interpretation: Naive Bayes combines probabilities across all features simultaneously, so even weak individual signals contribute real combined evidence. Information Gain measures each feature in isolation and cannot capture this combined/interactive effect — demonstrates a known limitation of univariate feature ranking methods.

## Feature Selection Test — Top 8 Information Gain Features (Decision Tree, CV, duration included)
- Same 8 features as above, same Cross Validation setup, swapped model to Decision Tree
- Full 18 features (DT, CV): 71.75% ± 2.30% accuracy, 93.67% yes-recall, 49.84% no-recall, 65.12% yes-precision
- Top 8 features (DT, CV): 79.11% ± 2.03% accuracy, 80.96% yes-recall, 77.25% no-recall, 78.07% yes-precision
- Key finding: trimming features DRAMATICALLY IMPROVED Decision Tree (+7.36pts accuracy, +27.41pts no-recall) — opposite effect to Naive Bayes, where the same trim slightly hurt performance
- This is the best-balanced model of the entire project — high accuracy with both classes recalled 77-81%
- Interpretation: Decision Tree likely overfit on the noisy 18-feature set, carving spurious splits on irrelevant attributes (default, day, marital, etc.). Trimming to genuinely predictive features reduced overfitting and let the tree focus splits on real signal. This is the opposite mechanism to Naive Bayes, which benefits from combining many weak signals rather than isolating strong ones — demonstrating that the value of feature selection is algorithm-dependent, not universal.

## Overall Conclusions (draft)
- Accuracy alone is a misleading metric for this dataset due to severe class imbalance — always paired with recall/precision
- Naive Bayes was the more stable, balanced performer across both phases (higher accuracy, lower variance, more even recall)
- Decision Tree (CV) pushed furthest toward catching subscribers (93.67% yes-recall) but at a steep cost to reliability (49.84% no-recall, highest variance)
- Model choice is a genuine business trade-off: DT-CV if missing a subscriber is very costly; NB if a balanced, reliable, generally-applicable model is preferred
- Call-behaviour/campaign features (duration, poutcome, month, contact, pdays) dominate predictive power; customer demographic/financial features (age, job, marital, education, balance) are consistently weak — subscription outcomes appear driven more by campaign execution than by who the customer is
- However, those "weak" demographic/financial features still contribute real combined value for Naive Bayes — removing them slightly hurt its performance, showing univariate rankings like Information Gain can't capture how multiple weak predictors combine in a probabilistic model
- For Decision Tree, the opposite held: removing the same weak features dramatically improved performance and balance (79.11% accuracy, 77-81% recall both classes) — the best-balanced model of the project — by reducing overfitting to noisy attributes
- Overall best model: Decision Tree with top-8 Information Gain features under Cross Validation (79.11% ± 2.03% accuracy, 80.96% yes-recall, 77.25% no-recall) — recommended as the final model if a single recommendation is required, given its strong balance across both classes and the highest overall accuracy achieved

## Still to do
- [x] Sensitivity test: Information Gain / models without `duration` (realistic pre-call-only scenario)
- [ ] Finalise figure numbering and captions
- [ ] Write up full report in CRISP-DM structure
- [ ] Repeat for group's 2nd dataset (Credit Default)