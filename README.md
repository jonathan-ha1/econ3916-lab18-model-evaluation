# Model Evaluation: The Curve, and What It Cannot Tell You

## Objective
This lab evaluates two classifiers on a rare outcome to see what ROC and precision-recall curves show under class imbalance, whether a single summary number can rank two close models, and how stated error costs turn an evaluation into a threshold decision.

## Methodology
- Simulated 8,000 cases of a rare outcome with five features and split them into 5,600 training rows and 2,400 test rows, stratified so both sets had the same share of positives, which came to 11.8% of the test rows.
- Fit a logistic regression pipeline on the training rows and drew its ROC curve and precision-recall curve on the test rows.
- Fit a gradient-boosted tree model on the same rows and compared the two models by ROC AUC, average precision, and precision at four levels of recall.
- Ran a paired bootstrap over 500 resamples of the test set to put a 95% interval around the AUC gap between the two models.
- Built an expected-cost curve where a missed positive costs ten times a false alarm, chose the cheapest threshold on the training rows using cross-validated probabilities, and priced it once on the test rows.
- Swept the cost ratio from 2:1 to 50:1 for both models, choosing each threshold the same way, and checked whether the cheaper model changed.
- Wrote a small function, roc_point(), that gives the false positive rate and true positive rate at any threshold.

## Key Findings
- The logistic regression had a ROC AUC of 0.847 but an average precision of only 0.465, because at low prevalence a small false positive rate still produces many false alarms compared to the few true positives.
- The boosted model had a ROC AUC of 0.8431, and the AUC difference on the full test set was +0.0038 with a 95% interval of [-0.0020, +0.0094], so since the interval includes zero the test set cannot say which model is better.
- The cheapest threshold chosen on the training rows was 0.0905, and on the test rows it cost 0.4692 per case against 0.9437 at the default 0.5, which cut the cost roughly in half.
- In the cost-ratio sweep the cheaper model switched depending on the ratio, but the gaps were small enough that neither model should be picked over the other without the business first stating its cost ratio.
- At the chosen threshold, roc_point() gave a false positive rate of 0.296 and a true positive rate of 0.823.
