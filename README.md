# IDRAK Project
# IDRAK | NLP Banking Intent Classification System

## 📌 Overview
**IDRAK** is an end-to-end Data Science and Natural Language Processing (NLP) project designed to automatically classify single-turn customer service inquiries into **77 fine-grained intent categories**. 

By automating query routing, this system helps banking support teams direct customers to the right department instantly. The project is built using the **Banking77 dataset (PolyAI)**, consisting of **13,083 customer service inquiries**.

## 📂 Dataset
- **Source:** [PolyAI Banking77 Dataset](https://github.com/PolyAI-LDN/task-specific-datasets)
- **Training Samples:** 10,003
- **Testing Samples:** 3,080
- **Total Classes:** 77 Intent Categories
- **Class Distribution:** Max 187 per class, Min 35 per class (Mean: 129.9)

## 🔬 Project Pipeline & Methodology

### Day 1: Data Understanding & Exploratory Data Analysis (EDA)
- **Data Quality Checks:** Verified zero missing values, zero duplicates, and zero data leakage between train/test splits.
- **Class Balance:** Analyzed the distribution of examples across all 77 categories to identify potential imbalance issues.
- **Text Exploration:** Calculated word and character counts (Average: ~11.9 words per message) and plotted the distribution of customer message lengths.
- **Domain Analysis:** Inspected semantically overlapping intents (e.g., `card_arrival` vs. `card_delivery_estimate`) to understand the classification difficulty.
- **Exported Data:** Saved cleaned datasets as `train_clean.csv` and `test_clean.csv`.

### Day 2: Baseline Modeling & Cross-Validation
- **Dummy Baseline:** Established a minimum performance floor using a Most Frequent Classifier (Accuracy: 1.3%).
- **Text Preprocessing & Modeling:** Built a reusable Scikit-Learn Pipeline:
  - **Vectorizer:** TF-IDF (ngram_range=(1,2), max_features=10000, sublinear_tf=True)
  - **Classifier:** Logistic Regression (C=5.0, max_iter=1000, class_weight='balanced')
- **Cross-Validation:** Used 5-Fold Stratified Cross-Validation to ensure model stability and check for overfitting (Mean CV Accuracy: 87.58% ± 0.61%).
- **Model Comparison:** Benchmarked Logistic Regression against a Linear SVM. Logistic Regression outperformed (89.42% vs. 89.06%).
- **Artifacts Exported:** Saved the trained pipeline (`banking_pipeline.joblib`) and class labels (`categories.joblib`).

### Day 3: Evaluation, Error Analysis & Predictions
- **Evaluation Metrics:**
  - **Top-1 Accuracy:** 89.42%
  - **Top-3 Accuracy:** 97.08% (Crucial for routing suggestions)
  - **Macro F1-Score:** 89.46%
- **Error Analysis:** Identified the top confused intent pairs (e.g., `why_verify_identity` misclassified as `verify_my_identity`, and `top_up_reverted` as `top_up_failed`). Visualized the top misclassifications.
- **Interactive Prediction:** Implemented a clean prediction cell demonstrating the model's ability to classify unseen customer queries with confidence scores.

