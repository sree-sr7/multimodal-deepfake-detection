# Multimodal Deepfake Audio Detection

Detecting AI-generated speech using a mid-fusion multimodal architecture 
combining acoustic and linguistic features.

## Results
| Metric | Value |
|--------|-------|
| Accuracy | 97.66% |
| EER (Known Attacks) | 2.36% |
| AUC | 0.9981 |
| EER (Zero-Day Attacks) | 8.20% |

## Architecture
- **Acoustic branch**: HuBERT (facebook/hubert-base-ls960) → 768-d embedding
- **Linguistic branch**: Whisper → DistilBERT → 768-d embedding  
- **Fusion**: Mid-fusion classifier (512 → 128 → 64 → 1)

## Dataset
[ASVspoof 2019 Logical Access](https://www.kaggle.com/datasets/awsaf49/asvpoof-2019-dataset)  
License: ODC Attribution License (ODC-By)

## Key Findings
- HuBERT acoustic features are the primary detection driver
- DistilBERT linguistic features provide 12.5% relative EER reduction
- Model generalizes to 13 unseen attack types (A07–A19) with 8.20% EER

## Environment
Trained on Kaggle (GPU T4 x2) using Python 3.12, PyTorch, HuggingFace Transformers
