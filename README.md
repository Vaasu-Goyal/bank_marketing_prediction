# Bank Marketing Campaign — Subscription Prediction

## Business Problem
Predicting which customers are likely to subscribe to a term deposit, 
to help a bank's marketing team target outreach more effectively and 
reduce wasted contact attempts.

## Dataset
UCI Bank Marketing dataset (45,211 records, 17 features)

## Approach
## Project Phases

### Phase 1 — Baseline Models
Established baseline performance using Decision Tree and Naive Bayes 
on minimally-processed data, to (a) confirm the dataset carries predictive 
signal, and (b) surface data quality issues empirically rather than assuming them.

Key finding: severe class imbalance caused deceptively high accuracy 
(88.99%) but poor minority-class recall (20.42%).

### Phase 2 — Refined Pipeline
Building on Phase 1's findings, applied targeted improvements:
- Class balancing (addressing the imbalance found in Phase 1)
- Feature engineering (pdays -1 handling)
- Cross-validation (more robust performance estimates)
- Information gain analysis (objective feature relevance)

This phase tests whether these improvements meaningfully outperform 
the Phase 1 baseline, and by how much.

## Results
[Your accuracy/precision numbers here, plus the decision tree screenshot]

## Key Insight
[Your 2-3 sentence business takeaway]
