# Chapter 3: Research Methodology

## 3.1 Introduction

This chapter presents the research methodology adopted to detect phishing emails using embeddings from Large Language Models (LLMs). The overall research philosophy is to design and deploy a system that uses contextual semantic representations learned from pre-trained transformer-based LLMs — specifically BERT, RoBERTa, and DistilBERT — to classify emails as phishing or legitimate with higher accuracy than traditional machine learning-based approaches.

The research design directly addresses the objectives presented in Chapter 1 and aligns with the gaps identified in Chapter 2, namely: reliance on shallow lexical features, susceptibility to sophisticated phishing attacks, inadequate semantic understanding, and lack of user-centric explainability.

---

## 3.2 Research Philosophy and Design

### 3.2.1 Research Philosophy

This research adopts a **post-positivist** philosophy. While it uses quantitative tests and measurements (e.g., F1-Score, AUC-ROC), it also considers the complexity of language, deception tactics, and attacker behavior inherent in phishing.

- **Ontology:** Phishing is an objective concept that can be identified, and its language features can be measured and modeled computationally.
- **Epistemology:** Knowledge is obtained through experiments, tests, and comparisons against benchmark datasets.

### 3.2.2 Research Design

A **quantitative research design** is used, following these steps:

1. Data collection and dataset preparation
2. Data feature extraction
3. Machine learning model training
4. Fine-tuning the best LLM model for phishing detection
5. Comparison between LLM models and traditional machine learning
6. Explainability analysis

### 3.2.3 Research Approach

A **deductive research approach** is used, starting from the theoretical proposition that contextual LLM embeddings are more effective at capturing semantic information than traditional TF-IDF or word-based representations (Salloum et al., 2022; Ariyadasa et al., 2023).

---

## 3.3 Dataset Acquisition and Description

### 3.3.1 Data Sources

Publicly available benchmark datasets are used to ensure ethical compliance. The datasets are summarised below:

| Dataset | Source | Size (Approx.) | Description |
|---|---|---|---|
| SpamAssassin Easy Ham | Apache SA | ~2,500 emails | Legitimate emails. Label = 0 |
| SpamAssassin Easy Ham 2 | Apache SA | ~1,400 emails | More legitimate emails. Label = 0 |
| SpamAssassin Spam | Apache SA | ~500 emails | Spam/phishing emails. Label = 1 |
| SpamAssassin Spam 2 | Apache SA | ~1,400 emails | More spam/phishing emails. Label = 1 |

The combined dataset is expected to contain at least **5,800 email samples**.

### 3.3.2 Email Content Components Used

The following components are extracted from each email:

- **Subject Line** – Often contains urgency, authority, and impersonation cues
- **Email Body** – Contains the main phishing content and social engineering
- **Sender Display Name** – May include impersonation content
- **Embedded URLs** – May contain phishing links

HTML tags, irrelevant headers, and binary attachments are excluded.

### 3.3.3 Data Preprocessing

**a) Text Extraction and Normalization** — Emails are parsed using Python's `email` library, HTML is cleaned with BeautifulSoup, and encoding issues are resolved using the `unicodedata` library.

**b) Deduplication** — Duplicate emails are removed to prevent data leakage.

**c) Language Filtering** — `langdetect` is used to retain only English-language emails, as the embedding models are primarily English.

**d) Text Truncation and Padding** — Input is truncated to 512 tokens (the transformer maximum). Shorter emails are padded with the tokenizer's special token.

**e) Label Assignment and Class Balancing** — Phishing emails are labeled 1; legitimate emails are labeled 0. SMOTE or class weighting is applied if class imbalance is detected.

**f) Dataset Splitting** — The dataset is randomly split into training, validation, and test sets. The test set is withheld until final evaluation.

---

## 3.4 Proposed System Architecture

The system is modular and end-to-end, comprising five main components:

```
[ INPUT: Raw Email (Subject + Body + URLs) ]
                        ↓
[ MODULE 1: Text Preprocessing Pipeline ]
                        ↓
[ MODULE 2: LLM Tokenisation & Embedding Extraction ]
         BERT-Base  |  RoBERTa-Base  |  DistilBERT
                        ↓
[ MODULE 3: Classification Module ]
  Logistic Regression | SVM | Fine-tuned LLM Head | Ensemble
                        ↓
[ MODULE 4: Evaluation & Comparison ]
     Accuracy | Precision | Recall | F1-Score | AUC-ROC | MCC
                        ↓
[ MODULE 5: Explainability Module (SHAP / Attention Visualisation) ]
```

---

## 3.5 LLM Embedding Models: Selection and Justification

Three transformer-based models were selected for comparative analysis:

| Feature | BERT-Base | RoBERTa-Base | DistilBERT |
|---|---|---|---|
| Parameters | 110M | 125M | 66M |
| Layers | 12 | 12 | 6 |
| Context | Bidirectional | Bidirectional | Bidirectional |
| Training Data | 16GB | 160GB+ | 16GB |
| Speed | Moderate | Slow | Fast |
| NLP Accuracy | High | Highest | ~97% of BERT |

### 3.5.1 BERT-Base

BERT uses bidirectional pre-training with Masked Language Modelling (MLM) and Next Sentence Prediction (NSP). It is used in two modes: (a) as a frozen feature extractor using the [CLS] token embedding, and (b) as a fine-tuned model with a classification head.

### 3.5.2 RoBERTa-Base

RoBERTa improves on BERT by removing the NSP objective, using dynamic masking, and training on a larger dataset (160GB+). It consistently achieves higher downstream accuracy but at a greater computational cost.

### 3.5.3 DistilBERT

DistilBERT achieves 97% of BERT's accuracy with 40% fewer parameters and 60% faster inference speed. It is particularly relevant for large-scale deployment scenarios.

### 3.5.4 Embedding Extraction Strategy

For each model, the text is tokenised (max 512 tokens) and the **[CLS] token output** from the final transformer encoder layer is used as the sentence-level embedding (768-dimensional for all three models). **Mean pooling** across all tokens will also be evaluated as an alternative strategy. Extraction is performed using the Hugging Face Transformers library in PyTorch.

---

## 3.6 Classification Models

### 3.6.1 Stage 1: Machine Learning Classifiers with Static Embeddings

**a) Logistic Regression** — With L2 regularization; used to assess the discriminative capability of the LLM embeddings.

**b) Support Vector Machine (SVM)** — With an RBF kernel; effective in high-dimensional (768-dim) feature spaces. Hyperparameters (C and gamma) are tuned using 5-fold cross-validation.

**c) TF-IDF Baseline** — TF-IDF vectorizer (max 10,000 features, bigrams) with Logistic Regression and SVM, replicating traditional NLP-based phishing detection approaches.

### 3.6.2 Stage 2: Fine-Tuned LLM Classification

The best-performing Stage 1 model is fine-tuned end-to-end with a linear classification head. Fine-tuning parameters:

- **Optimizer:** AdamW with linear learning rate warmup (first 10% of steps)
- **Learning rate:** 2e-5
- **Batch size:** 32
- **Max epochs:** 5
- **Early stopping:** Based on F1 score
- **Gradient clipping:** Applied to prevent exploding gradients

### 3.6.3 Ensemble Model

A soft voting ensemble of the top two LLM models is evaluated to exploit model diversity and improve performance on borderline cases (Dietterich, 2000).

---

## 3.7 Experimental Design

| Exp. | Embedding Model | Classifier | Objective |
|---|---|---|---|
| 1 | TF-IDF (Baseline) | Logistic Regression, SVM | Baseline traditional ML performance |
| 2 | BERT-Base Embeddings | Logistic Regression, SVM | Evaluate BERT contextual representations |
| 3 | RoBERTa-Base Embeddings | Logistic Regression, SVM | Evaluate optimised BERT variant performance |
| 4 | DistilBERT Embeddings | Logistic Regression, SVM | Evaluate lightweight model trade-offs |
| 5 | Best LLM (Fine-tuned) | Fine-tuned Classification Head | Optimise best performing model end-to-end |
| 6 | Ensemble (BERT + RoBERTa) | Voting / Stacking | Evaluate combined model improvement |

Each experiment uses the same dataset split and evaluation metrics. Results are averaged over three runs with different random seeds, and standard deviation is reported.

---

## 3.8 Evaluation Framework

### 3.8.1 Performance Metrics

| Metric | Formula | Relevance to Phishing Detection |
|---|---|---|
| Accuracy | (TP + TN) / (TP + TN + FP + FN) | Overall classification correctness |
| Precision | TP / (TP + FP) | Minimises false positives (legitimate emails flagged) |
| Recall | TP / (TP + FN) | Minimises missed phishing emails |
| F1-Score | 2 × (Precision × Recall) / (Precision + Recall) | Balanced measure for imbalanced classes |
| AUC-ROC | Area under ROC Curve | Discriminative ability across thresholds |
| MCC | Correlation between predicted & actual classes | Robust metric for imbalanced datasets |

The **F1-Score** is the primary metric, given the cost of both false positives and false negatives in phishing detection.

### 3.8.2 Confusion Matrix Analysis

Confusion matrices will be generated for each experiment to identify error patterns across models (e.g., whether BERT produces more false positives than RoBERTa).

### 3.8.3 Statistical Significance Testing

**McNemar's test** is used to assess whether performance differences between models are statistically significant (threshold: p < 0.05) (Demšar, 2006).

### 3.8.4 Cross-Validation

**5-fold cross-validation** is used on the training set. The test set is held out until final evaluation.

---

## 3.9 Explainability and User Awareness Module

### 3.9.1 SHAP (SHapley Additive Explanations)

SHAP values are computed for Stage 1 classifiers to measure the contribution of individual embedding dimensions and input tokens to the classification decision. The SHAP library's Text Explainer module is used.

### 3.9.2 Attention Visualization

Attention weights from the final transformer encoder layer are aggregated across all attention heads and mapped to input tokens, producing a saliency map that highlights the most influential tokens in the classification decision.

### 3.9.3 User-Friendly Explanation Prototype

A simple prototype will be developed to display phishing emails alongside highlighted key features (using SHAP and attention values), enabling users to understand what makes an email suspicious — improving user awareness (Kumaraguru et al., 2010; Arachchilage & Love, 2014).

---

## 3.10 Tools and Technologies

All experiments are implemented in **Python 3.10** using the following libraries:

- **Hugging Face Transformers (v4.x)** — Pre-trained BERT, RoBERTa, and DistilBERT models and tokenizers
- **PyTorch (v2.x)** — Deep learning backend for fine-tuning and inference
- **Scikit-learn (v1.x)** — TF-IDF baseline, Logistic Regression, SVM, cross-validation, and metrics
- **BertViz / BerTopic** — Attention visualization and topic-level analysis
- **Pandas & NumPy** — Data manipulation and numerical computation
- **Matplotlib & Seaborn** — Visualization of results, confusion matrices, and performance plots
- **BeautifulSoup4** — HTML email parsing and text extraction
- **NLTK / SpaCy** — Text normalization and linguistic feature extraction

---

## 3.11 Ethical Considerations

### 3.11.1 Data Privacy and Anonymization

All personally identifiable information (PII) — including email addresses, names, and IP addresses — will be removed during preprocessing using automated named entity recognition. The anonymized dataset will be handled in accordance with **UK GDPR** data minimization principles.

### 3.11.2 Bias and Fairness

Error patterns will be analyzed across subgroups (e.g., non-native English speakers) to detect and mitigate systematic bias. Class balancing techniques will be applied as needed (Mehrabi et al., 2021).

### 3.11.3 Dual Use Risk

In line with responsible disclosure principles, the research focuses exclusively on defensive applications and will not publish technical details exploitable by attackers. Model weights and code will be shared for academic and institutional use only.

### 3.11.4 Environmental Impact

The environmental cost of training transformer models will be tracked and reported using the **CodeCarbon** library (Courty et al., 2021). The inclusion of DistilBERT was partly motivated by its lower environmental footprint (Strubell et al., 2019).

### 3.11.5 Institutional Ethics Approval

As the research uses only public datasets and does not involve human participants, no special ethics committee approval is required. The study will comply fully with the university's ethical guidelines.

---

## References

- Arachchilage, N. A. G., & Love, S. (2014). Security awareness of computer users: A phishing threat avoidance perspective. *Computers in Human Behavior*, 38, 304–312.
- Creswell, J. W., & Creswell, J. D. (2018). *Research design: Qualitative, quantitative, and mixed methods approaches* (5th ed.). SAGE Publications.
- Demšar, J. (2006). Statistical comparisons of classifiers over multiple data sets. *Journal of Machine Learning Research*, 7, 1–30.
- Devlin, J., Chang, M. W., Lee, K., & Toutanova, K. (2019). BERT: Pre-training of deep bidirectional transformers for language understanding. *NAACL-HLT 2019*, 4171–4186.
- Dietterich, T. G. (2000). Ensemble methods in machine learning. *MCS 2000*, 1–15.
- Kumaraguru, P., Sheng, S., Acquisti, A., Cranor, L. F., & Hong, J. (2010). Teaching Johnny not to fall for phish. *ACM Transactions on Internet Technology*, 10(2), 1–31.
- Liu, Y., et al. (2019). RoBERTa: A robustly optimized BERT pretraining approach. *arXiv:1907.11692*.
- Mehrabi, N., et al. (2021). A survey on bias and fairness in machine learning. *ACM Computing Surveys*, 54(6), 1–35.
- Salloum, S., et al. (2022). A systematic literature review on phishing email detection using NLP. *IEEE Access*, 10, 65703–65727.
- Sanh, V., et al. (2019). DistilBERT, a distilled version of BERT. *arXiv:1910.01108*.
- Strubell, E., Ganesh, A., & McCallum, A. (2019). Energy and policy considerations for deep learning in NLP. *ACL 2019*, 3645–3650.
- Wolf, T., et al. (2020). Transformers: State-of-the-art natural language processing. *EMNLP 2020*, 38–45.
