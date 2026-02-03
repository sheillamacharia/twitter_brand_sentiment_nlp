# 📊 Twitter Sentiment Analysis for Brand Monitoring  
**Apple & Google Products**

---

## Project Overview

Social media platforms generate massive volumes of unstructured user feedback every day. For technology brands such as **Apple** and **Google**, understanding public sentiment at scale is critical for monitoring brand perception, identifying customer dissatisfaction, and supporting data-driven decision-making.

This project builds a **proof-of-concept Natural Language Processing (NLP) pipeline** that automatically classifies tweets as expressing **positive or negative sentiment** toward brands and products. The goal is not deployment, but to demonstrate a complete, interpretable, and business-aligned sentiment analysis workflow.

---

## Business Problem

Manual analysis of social media sentiment is impractical due to:
- High data volume
- Informal and noisy language
- Rapidly changing public opinion

The business objective is to **automatically detect sentiment polarity** in tweets related to Apple and Google products in order to:
- Monitor overall brand perception
- Identify emerging negative feedback
- Support marketing, customer service, and product teams with timely insights

---

## Dataset

**Judge Emotion About Brands and Products Dataset**

- **Source:** Crowdsourced Twitter sentiment annotations
- **Size:** ~9,000 tweets
- **Key Fields:**
  - `tweet_text`: Raw tweet content
  - `is_there_an_emotion_directed_at_a_brand_or_product`: Sentiment label
  - `emotion_in_tweet_is_directed_at`: Brand or product (largely missing)

### Data Challenges
- Significant class imbalance (neutral dominates)
- Informal language, slang, abbreviations
- Ambiguous sentiment in short text
- Heavy brand skew toward Apple products

---

## Modeling Assumptions

This project operates under the following assumptions:

1. Neutral and ambiguous tweets provide limited immediate business value and are excluded.
2. Explicit lexical cues are sufficient for high-level sentiment detection in a proof-of-concept.
3. Misclassification costs are assumed to be symmetric due to lack of explicit business cost constraints.
4. Brand dominance (Apple-heavy data) is acceptable for demonstrating methodology, but limits generalizability.

---

## Data Preparation

### Cleaning Steps
- Removal of tweets with missing text
- Exclusion of ambiguous sentiment (“I can’t tell”)
- Binary sentiment encoding:
  - `1` → Positive
  - `0` → Negative

### Text Normalization
- Lowercasing
- Removal of URLs, mentions, and non-alphabetic characters
- Stopword removal (negations preserved)
- Lemmatization using NLTK

---

## Feature Engineering

Text is converted into numerical features using **TF-IDF (Term Frequency–Inverse Document Frequency)**.

**Design choices:**
- Unigrams only
- Maximum feature cap to control sparsity
- Suitable for linear models and interpretability

TF-IDF was selected for its balance between performance, transparency, and efficiency on small-to-medium NLP datasets.

---

## Modeling Approach

Three baseline models were evaluated:

| Model | Rationale |
|------|----------|
| Logistic Regression | Interpretable, robust baseline |
| Multinomial Naive Bayes | Fast, text-specific probabilistic model |
| Linear Support Vector Classifier (LinearSVC) | Strong performance on sparse, high-dimensional text |

### Validation Strategy
- 80/20 stratified train-test split
- Fixed random state for reproducibility
- Evaluation focused on recall, precision, F1-score, and confusion matrices

Accuracy alone was avoided due to class imbalance.

---

## Model Performance Summary

| Model | Accuracy | Negative Recall | Key Limitation |
|------|---------|----------------|----------------|
| Logistic Regression | 0.85 | 0.69 | Misses some negative sentiment |
| Naive Bayes | 0.85 | 0.04 | Fails to detect negative tweets |
| **LinearSVC (Final)** | **0.88** | **0.61** | Moderate false positives |

**Final Model:** LinearSVC

---

## Error Analysis

Qualitative inspection of misclassified tweets revealed:

- **False Positives:** Neutral or factual tweets with brand mentions
- **False Negatives:** Subtle or implicit positive sentiment

These errors are consistent with known limitations of bag-of-words models and informal social media text.

---

## Feature Importance & Interpretability

LinearSVC coefficients were analyzed to understand decision drivers.

- **Positive indicators:** `great`, `awesome`, `cool`, `fun`, `winning`
- **Negative indicators:** `fail`, `hate`, `crash`, `headache`, `disappointment`

The model learns intuitive sentiment-bearing patterns rather than relying solely on brand names.

---

## Threshold & Decision Boundary

LinearSVC applies a default decision boundary at zero on the signed distance from the hyperplane.

The default threshold was retained because:
- Business costs were not explicitly asymmetric
- Performance metrics were already balanced
- Probability calibration was out of scope

---

## Business-Aligned Validation

From a business perspective:
- Missing negative sentiment poses reputational risk
- False positives are less costly and manageable

The model supports:
- High-level brand sentiment monitoring
- Trend analysis
- Analyst-assisted review of flagged tweets

---

## Limitations

- Bag-of-words representation ignores context and sarcasm
- Class imbalance biases predictions toward positive sentiment
- Emoji and metadata are not incorporated
- Neutral sentiment is excluded
- Brand representation is skewed toward Apple products

---

## Recommendations

- Incorporate n-grams and emoji-aware preprocessing
- Explore contextual embeddings (e.g., transformer-based models)
- Reintroduce neutral sentiment as a third class
- Apply cost-sensitive learning for negative sentiment
- Integrate human-in-the-loop validation

---

## Executive Summary

This project demonstrates a complete and interpretable NLP sentiment analysis pipeline capable of extracting actionable brand perception signals from noisy social media data. While not production-ready, the model provides a strong foundation for scalable brand sentiment monitoring and future refinement.
