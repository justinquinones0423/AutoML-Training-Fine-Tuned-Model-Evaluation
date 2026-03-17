# Week 5: AutoML & No-Code Model Training
Trained a custom image classifier with Google Teachable Machine and compared
generic vs fine-tuned Hugging Face models for the ingestion component
of our Security Alert Triage Bot capstone project.
## Custom Model Training
- Built a Phishing/Legitimate image classifier with Teachable Machine
- Achieved 60% accuracy on 10 held-out test images
- Precision: 66.7% | Recall: 40% | F1: 50%
## Fine-Tuned Model Comparison
Compared 3 models (1 generic + 2 fine-tuned) on 5 test inputs:
- Generic: distilbert-sst-2 (sentiment)
- Fine-Tuned A: cybersectony/phishing-email-detection-distilbert_v2.4.1
- Fine-Tuned B: ealvaradob/bert-finetuned-phishing
## Finding
Recommended ealvaradob/bert-finetuned-phishing] for ingestion component because for the Security Alert Triage Bot project, it uses domain-specific cybersecurity labels (phishing/benign) that are directly actionable for threat triage. Fine-tuned models showed higher performance with more relevant labels and better handling of real threat scenarios like brute force attacks..
Fine-tuned models showed higher performance with more relevant labels and higher confidence correctly identifying the SSH brute force attack (Record 4) with near-perfect confidence while the generic model failed to distinguish it from routine activity.
See `report.md` for full analysis
