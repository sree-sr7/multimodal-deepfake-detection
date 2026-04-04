# Multimodal Deepfake Audio Detection

Detecting AI-generated speech using a mid-fusion multimodal architecture
combining acoustic and linguistic features.

## Results
| Metric | Value |
|--------|-------|
| Accuracy (Known Attacks A01–A06) | 97.66% |
| EER (Known Attacks) | 2.36% |
| AUC (Known Attacks) | 0.9981 |
| Accuracy (Unseen Attacks A07–A19) | 92.20% |
| EER (Zero-Day Attacks) | 8.20% |
| AUC (Unseen Attacks) | 0.9739 |

## Architecture
- **Acoustic branch**: HuBERT (facebook/hubert-base-ls960) → 768-d embedding
- **Linguistic branch**: Whisper → DistilBERT → 768-d embedding
- **Fusion**: Mid-fusion classifier (512 → 128 → 64 → 1)

## Methodology
- **Speaker-disjoint splits**: Dataset divided 70/15/15 at the speaker level — no speaker appears in more than one split, guaranteeing zero data leakage between train, val, and test
- **5-fold cross-validation**: Folds defined over speakers (not samples) for statistically robust evaluation
- **Balanced sampling**: Equal real/spoof samples per split to prevent class imbalance bias
- **Zero-day generalization**: Model trained only on A01–A06 attack types, then evaluated on 13 completely unseen attack types (A07–A19) from the evaluation set

## Ablation Study
| Scenario | EER |
|----------|-----|
| Full Mid-Fusion (Acoustic + Linguistic) | 0.00% |
| Acoustic Only (HuBERT) | 2.85% |
| Linguistic Only (DistilBERT) | 51.81% |

HuBERT acoustic features are the primary detection driver. DistilBERT linguistic features provide a 12.5% relative EER reduction via multimodal fusion.

## Per-Attack Type EER Breakdown
| Attack ID | Technology | EER |
|-----------|-----------|-----|
| A01 | Neural TTS | 0.23% |
| A02 | Vocoder TTS | 0.31% |
| A03 | Vocoder TTS Neural | 0.31% |
| A04 | Waveform Concatenation | 0.54% |
| A05 | Vocoder VC Neural | 0.04% |
| A06 | Vocoder VC Linear | 4.50% |

## Dataset
[ASVspoof 2019 Logical Access](https://www.kaggle.com/datasets/awsaf49/asvpoof-2019-dataset)
License: ODC Attribution License (ODC-By)

## Environment
Trained on Kaggle (GPU T4 x2) | Python 3.12 | PyTorch | HuggingFace Transformers
