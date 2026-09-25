# Bank Marketing Campaign — Subscription Prediction

## Business Problem
Predicting which customers are likely to subscribe to a term deposit, 
to help a bank's marketing team target outreach more effectively and 
reduce wasted contact attempts.

## Dataset
UCI Bank Marketing dataset (45,211 records, 17 features)

## Approach
- Data cleaning: handled "unknown" categorical values, addressed class imbalance
- Compared Decision Tree vs Naive Bayes classifiers in RapidMiner
- Key finding: excluding `duration` (unknown before a call happens) gives 
  a more realistic, deployable model despite lower raw accuracy

## Results
[Your accuracy/precision numbers here, plus the decision tree screenshot]

## Key Insight
[Your 2-3 sentence business takeaway]
