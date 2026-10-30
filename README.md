# Payment Fraud Detection with Python (PaySim)

A fraud detection project in Python on the public PaySim dataset (about 6.4 million simulated mobile-money transactions). It compares simple rules with machine learning models, tests the best model on a later period it has never seen, and turns the result into a number a fraud team can use: how much fraud is caught if only the top part of transactions is reviewed.

The notebook with all code, charts and results is here: [`payment_fraud_model.ipynb`](payment_fraud_model.ipynb)

## What the notebook does

1. **Loads and checks the data** in chunks to save memory, and shows how rare fraud is.
2. **Finds where the fraud is:** fraud rate by transaction type, and which types can be left out of the model.
3. **Builds features:** balance checks (does the balance change by exactly the amount?), account-emptying patterns, hour of day, and transaction type.
4. **Splits the data by time** (train, validation, test), not at random, as a real model is trained on the past and used on the future.
5. **Handles the very low share of fraud** by comparing logistic regression, gradient boosting, and gradient boosting with class weights, using average precision instead of accuracy.
6. **Chooses the alert threshold on the validation set** and uses the test set once.
7. **Compares the model with two rules:** the system's own fraud flag, and a one-line "account emptied" rule.
8. **Shows review workload:** the share of fraud (by count and by amount) caught if only the top 0.05% to 2% of transactions by score are reviewed.
9. **Explains the model** with permutation importance.
10. **Saves tables for a dashboard** (see `outputs/`).

## Outputs

Running the notebook creates these files in `outputs/`, ready to load into a dashboard tool such as Looker Studio or Power BI:

| File | Content |
|---|---|
| `fraud_by_type.csv` | Transactions, fraud count, fraud amount and fraud rate by transaction type |
| `fraud_by_hour.csv` | Fraud rate by hour of the (simulated) day |
| `fraud_by_amount_band.csv` | Fraud rate by amount band |
| `approach_comparison.csv` | Model and rules: alerts, caught fraud, false alarms, precision, recall |
| `review_workload.csv` | Fraud caught for each review budget |
| `test_daily_monitoring.csv` | Per day in the test period: transactions, fraud, alerts, caught fraud |

## Data

The data is **PaySim**, a public simulated dataset: "Synthetic Financial Datasets For Fraud Detection" on Kaggle. Download the CSV file and put it in the `data/` folder. The file is not stored in this repository. It has 11 columns: `step`, `type`, `amount`, `nameOrig`, `oldbalanceOrg`, `newbalanceOrig`, `nameDest`, `oldbalanceDest`, `newbalanceDest`, `isFraud`, `isFlaggedFraud`.

## How to run

```bash
pip install -r requirements.txt
jupyter notebook payment_fraud_model.ipynb
```

Then choose **Run all**. The full file is easier to run with 8 GB of memory or more, and it can take several minutes. To try the notebook on a smaller part first, set `MAX_ROWS = 1_000_000` in the first code cell.

## Limits

- PaySim is **simulated**. Its fraud follows simple patterns, so scores are higher than they would be on real payments. The project shows the method, not how a model would work on real data.
- A real system would use more information: customer history, device, location and merchant.
- The alert threshold is chosen for the best F1 score. A real team would choose it from the cost of a missed fraud against the cost of a false alarm.

## Related

The same data is analysed with SQL in [aml-fraud-detection-sql](https://github.com/alish22/aml-fraud-detection-sql).

## Tools

Python, pandas, NumPy, scikit-learn, Matplotlib, Jupyter.
