# Week 5 Report: AutoML Training & Fine-Tuned Model Evaluation

**Name:** Justin Quinones
**Date:** 3/16/2026
**Capstone Project:** Security Alert Triage Bot
**My Component:** Ingestion

## Part A: Teachable Machine Training

### Training Setup
- **Task:** Phishing vs Legitimate email screenshot classification
- **Training images per class:** 20
- **Test images per class:** 5
- **Total training time:** 5 Seconds

### Test Results

| # | Actual Class | Predicted Class | Confidence | Correct? |
|---|--------------|-----------------|------------|----------|
| 1 | Legitimate | Legitimate | 53% | Yes |
| 2 | Legitimate | Phishing | 62% | No |
| 3 | Legitimate | Legitimate | 53% | Yes |
| 4 | Legitimate | Legitimate | 58% | Yes |
| 5 | Legitimate | Legitimate | 69% | Yes |
| 6 | Phishing | Legitimate | 66% | No |
| 7 | Phishing | Legitimate | 65% | No |
| 8 | Phishing | Phishing | 84% | Yes |
| 9 | Phishing | Phishing | 87% | Yes |
| 10 | Phishing | Legitimate | 57% | No |

### Confusion Matrix

| | Predicted: Phishing | Predicted: Legitimate |
|---|---|---|
| **Actual: PHISHING** | TP = 2  | FN = 3 |
| **Actual: LEGITIMATE** | FP = 1 | TN = 4 |

### Calculated Metrics
- **Accuracy:** 60%
- **Precision:** 66.7%
- **Recall:** 40%
- **F1 Score:** 50%

### Interpretation
My model is better at precision than recall, meaning that it is fairly reliable when it does flag an email as phishing, but it missed the majority of actual phishing emails classifying 2 out of 5 as legitimate. The most common error was false negatives, where phishing emails were incorrectly labeled as legitimate, which is the more dangerous in a real cybersecurity context since threats go undetected. To improve the model, adding more diverse phishing training images and increasing the dataset from 20-25 to 50+ images per class would likely help the model generalize better and reduce missed detections. 

---

## Part B: Generic vs Fine-Tuned Model Comparison

### Models Tested
1. **Generic:** distilbert-base-uncased-finetuned-sst-2-english (sentiment)
2. **Fine-Tuned A:** cybersectony/phishing-email-detection-distilbert_v2.4.1 — This model detects phishing emails using DistilBERT. 
3. **Fine-Tuned B:** ealvaradob/bert-finetuned-phishing — This model detects phishing URLs/emails using BERT. 

### Results

| Input | Generic Label (Score) | Fine-Tuned A Label (Score) | Fine-Tuned B Label (Score) | Best Model |
|-------|-----------------------|-----------------------------|---------------------------|------------|
| Record 1 | NEGATIVE (0.9959) | LABEL_0 (0.9999) | benign (0.9990) | None |
| Record 2 | NEGATIVE (0.9986) | LABEL_0 (1.0000) | benign (1.0000) | Fine-Tuned B |
| Record 3 | NEGATIVE (0.9958) | LABEL_0 (0.9980) | benign (0.9948) | None |
| Record 4 | NEGATIVE (0.9996) | LABEL_1 (0.9997) | phishing (1.0000) | Fine-Tuned A & B |
| Record 5 | Negative (0.9962) | LABEL_0 (0.9993) | Phishing (0.9919) | Fine-Tuned A |

### Analysis

**Generic model strengths:** The generic sentiment model consistently returned high-confidence scores and never errored out. It performed adequately on clearly routine inputs (Record 2) by labeling them NEGATIVE, which loosely aligns with "non-threatening."

**Generic model weaknesses:** The generic model was not designed for cybersecurity and labeled every single input as NEGATIVE regardless of whether it was a real threat or routine activity. It cannot distinguish between a phishing attack and a firewall update making it essentially useless for threat detection.

**Fine-tuned model advantage:** Both fine-tuned models used domain-relevant labels (phishing/benign, LABEL_0/LABEL_1) and correctly identified the SSH brute force attack (Record 4) as a threat with near-perfect confidence. Fine-Tuned A also correctly classified the normal system activity (Record 5) as safe, showing better precision than Fine-Tuned B.

**Biggest surprise:** The result I did not expect was that the Fine-Tuned B incorrectly flagged normal system activity (Record 5) as phishing with 99% confidence. A false positive that shows even domain-specific models can be overfit and unreliable on ambiguous inputs.

### Recommended Model for My Capstone Component
**Component:** Ingestion

**Primary model:** [ealvaradob/bert-finetuned-phishing — This model uses domain-specific cybersecurity training and returns labels directly relevant to threat triage (phishing/benign/, making it far more actionable than a generic sentiment model for a security alert bot.

**Confidence threshold:** Given that both fine-tuned models operated with extremely high confidence across all records (often 0.999+), a threshold of 99% is appropriate for auto-action.Anything below 99% should be routed to human review, since in a security triage context a missed threat is far more costly than a delayed one.

**Priority metric:** Recall matters for my project. In a security alert triage bot, the most critical failure is a missed threat (false negative). Missing a real phishing attack or intrusion attempt can lead to a data breach, so the model must catch as many true threats as possible even at the cost of generating some false alarms. Precision matters, but recall takes priority when the stakes of missing a threat are high.

---

## Limitations & Next Steps

The primary limitation of this evaluation is the small test set with only 5 records which is not enough to draw statistically reliable conclusions about model performance. With more time, I would expand the dataset to 50–100 records covering a wider variety of threat types, including insider threats, malware alerts, and social engineering attempts beyond just phishing. I would also fine-tune my own model using labeled security incident data specific to the triage bot's use case, rather than relying on general phishing detection models that may not generalize well to all alert types. 
