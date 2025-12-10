# 🛡️ Credit Card Fraud Detection Analysis

## Overview
This project performs a deep-dive analysis on anonymized credit card transaction data to identify potential fraud. Unlike traditional supervised learning approaches that require labeled "fraud" examples, this project utilizes **Unsupervised Learning (Isolation Forest)** to detect anomalies based on statistical deviations and behavioral patterns.

The goal is to identify significantly different transactions and explain *why* they are suspicious, providing actionable insights for fraud prevention.

## Dataset Description
The dataset consists of 7 anonymized columns representing transaction metadata:

| Column | Description | Key Insight |
| :--- | :--- | :--- |
| **Col 1** | Distance from Home | Distance of the current transaction from the cardholder's base (kms). |
| **Col 2** | Distance from Last Transaction | The "Jump" or distance traveled since the previous transaction (kms). |
| **Col 3** | Value Ratio | Transaction value compared to the user's average (e.g., 5.0 = 5x normal spend). |
| **Col 4** | Merchant History | `1` = Repeat Merchant, `0` = New Merchant. |
| **Col 5** | Approval Method 1 | Type of approval mechanism used (e.g., Chip/PIN). |
| **Col 6** | Approval Method 2 | Alternative approval mechanism (correlated with higher risk). |
| **Col 7** | Transaction Type | General classification (likely Online vs. Offline). |

## Methodology & Approach

### 1. Feature Engineering (The "Secret Sauce")
To improve the model's accuracy, we created three specific interaction features based on domain knowledge:
* **`Risky_Combo`**: Flags transactions that occur at a **New Merchant** (`Col 4=0`) using **Approval Type 2** (`Col 6=1`). *Analysis showed this combination has a 99% anomaly rate.*
* **`Suspicious_Jump`**: Flags "Impossible Travel" where the distance from the last transaction (`Col 2`) exceeds 50km without a time buffer.
* **`Dist_Value_Interaction`**: Multiplies Distance (`Col 1`) by Value (`Col 3`) to catch high-value purchases made far from home (a common fraud tactic to avoid immediate detection).

### 2. The Model: Isolation Forest
We used an **Isolation Forest** algorithm. This method isolates observations by randomly selecting a feature and then randomly selecting a split value.
* **Why used?** Fraudulent transactions are few and different. They are easier to "isolate" (require fewer splits) than normal transactions.
* **Configuration:** `contamination=0.05` (Assuming ~5% of data is anomalous).

### 3. Explainability
The code generates a "Reason" for every flagged transaction, such as:
* *"Suspicious Local Pattern"*
* *"High Distance / Impossible Travel"*
* *"Abnormal High Value"*

##  Installation & Usage

### Prerequisites
Ensure you have Python installed with the following libraries:
```bash
pip install pandas numpy scikit-learn
