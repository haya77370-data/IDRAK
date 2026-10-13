# IDRAK | NLP Banking Intent Classification System

## The problem. What are we trying to solve?
Customer service teams in the banking sector receive thousands of inquiries daily. Routing these messages to the correct department manually is slow, error-prone, and expensive. 

We aim to build an automated system that classifies **single-turn customer service banking messages** into **77 fine-grained intent categories** using natural language text. By automating this routing process, we can significantly reduce response times and improve operational efficiency. 

**Evaluation Metrics:** 
- Macro F1-Score (to penalize misclassifications across subtle classes equally)
- Accuracy

## The data. What did we discover?
We used the **Banking77 dataset (PolyAI)**, which consists of **13,083 customer service inquiries**.
- **Train samples:** 10,003
- **Test samples:** 3,080
- **Number of classes:** 77

**Key Discoveries:**
- **Data Quality:** The dataset is exceptionally clean. We verified zero missing values, zero duplicates, and zero data leakage between the train and test sets.
- **Class Balance:** The dataset is relatively balanced, with an average of 129.9 examples per class (Min: 35, Max: 187). This means we don't need to apply aggressive resampling techniques.
- **Text Characteristics:** Customer queries are short and concise. The average message length is 11.9 words (max: 79 words). 
- **Domain Challenge:** There is significant semantic overlap between certain intent categories. For example, `card_arrival` vs. `card_delivery_estimate` use very similar vocabulary, making them inherently difficult for a model to distinguish.

## The approach. What did we try?
Our approach followed a rigorous, leak-free machine learning pipeline:

1. **Dummy Baseline:** We first established a minimum performance floor using a `DummyClassifier` (Most Frequent strategy). This yielded a mere 1.3% accuracy, confirming that a naive approach is insufficient.
2. **Integrated Text Preprocessing & Modeling:** We built a reusable Scikit-Learn Pipeline to ensure zero data leakage during cross-validation:
   - **Vectorization:** TF-IDF (ngram_range=(1, 2), max_features=10000, sublinear_tf=True)
   - **Classifier:** Logistic Regression (C=5.0, max_iter=1000, class_weight='balanced', random_state=42)
3. **Cross-Validation:** We performed 5-Fold Stratified Cross-Validation to confirm model stability and check for overfitting.
4. **Model Comparison:** We benchmarked Logistic Regression against a Linear SVM to see which architecture handled the text classification task better.
5. **Error Analysis:** We conducted a deep dive into the misclassified queries to identify the top confused intent pairs (e.g., `why_verify_identity` being predicted as `verify_my_identity`).
6. **Model Export:** We serialized the final pipeline and class labels (`banking_pipeline.joblib`, `categories.joblib`) for deployment and future inference.

## Results.
Our final Logistic Regression model significantly outperformed the baseline and the SVM alternative. 

| Metric | Score |
| :--- | :--- |
| Dummy Baseline Accuracy | 1.30% |
| CV Mean Accuracy | 87.58% (± 0.61%) |
| Test Accuracy (Top-1) | 89.42% |
| **Test Accuracy (Top-3)** | **97.08%** |
| Macro Precision | 89.87% |
| Macro Recall | 89.42% |
| Macro F1-Score | 89.46% |

**Key Outcomes:**
- **Model Comparison:** Logistic Regression (89.42% Acc / 89.46% F1) outperformed Linear SVM (89.06% Acc / 89.06% F1).
- **Top-3 Accuracy:** The model achieves a remarkable **97.08% Top-3 accuracy**, meaning the correct intent is within the top 3 predictions 97% of the time. This makes it highly practical for a human-in-the-loop routing system where an agent can quickly select from the top 3 suggestions.
- **Error Patterns:** Most errors occur between highly similar intents (e.g., `top_up_reverted` → `top_up_failed`), which is expected given the fine-grained nature of the 77 classes.
- **Interactive Prediction:** The model successfully predicts unseen queries with confidence scores (e.g., classifying "My credit card was stolen while traveling in London" as `Lost Or Stolen Card` with 27.3% confidence).
