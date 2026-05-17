# Multimodal Deepfake Audio Detection

AI-generated voice cloning is an escalating threat to biometric security systems,
enabling identity fraud, deepfake scams, and unauthorized access. This project
builds a multimodal countermeasure system that detects synthetic speech by fusing
acoustic and linguistic signals, mimicking how humans cross-check *what* was said
with *how* it was said.

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
- **Speaker-disjoint splits**: Dataset divided 70/15/15 at the speaker level — no
  speaker appears in more than one split, guaranteeing zero data leakage between
  train, val, and test
- **5-fold cross-validation**: Folds defined over speakers (not samples) for
  statistically robust evaluation
- **Balanced sampling**: Equal real/spoof samples per split to prevent class
  imbalance bias
- **Zero-day generalization**: Model trained only on A01–A06 attack types, then
  evaluated on 13 completely unseen attack types (A07–A19) from the evaluation set

## Ablation Study
| Scenario | EER | Notes |
|----------|-----|-------|
| Full Mid-Fusion (Acoustic + Linguistic) | 0.00% | Best performance |
| Acoustic Only (HuBERT) | 2.85% | Primary driver |
| Linguistic Only (DistilBERT) | 51.81% | Coin-flip without acoustics |

**Key insight**: HuBERT acoustic features are the primary detection driver.
Adding DistilBERT linguistic features reduces the overall validation EER from
2.36% to near zero — a 12.5% relative improvement. However, dropping the
linguistic branch (acoustic-only) still yields a competitive 2.85% EER at
significantly lower compute cost, making it a viable trade-off for
latency-sensitive or resource-constrained deployments.

## Per-Attack Type EER Breakdown
| Attack ID | Technology | EER |
|-----------|------------|-----|
| A01 | Neural TTS | 0.23% |
| A02 | Vocoder TTS | 0.31% |
| A03 | Vocoder TTS Neural | 0.31% |
| A04 | Waveform Concatenation | 0.54% |
| A05 | Vocoder VC Neural | 0.04% |
| A06 | Vocoder VC Linear | 4.50% |

A06 (Linear Vocoder VC) is the hardest attack to detect, as linear vocoders
produce more natural-sounding prosody that closely mimics human speech patterns.

## Dataset
[ASVspoof 2019 Logical Access](https://www.kaggle.com/datasets/awsaf49/asvpoof-2019-dataset)
License: ODC Attribution License (ODC-By)

## Environment
Trained on Kaggle (GPU T4 x2) | Python 3.12 | PyTorch | HuggingFace Transformers

## Limitations & Future Work
- **Compute Constraints:** Due to Kaggle free-tier GPU quotas and session time limits, the model was trained on a strategically balanced subset of the ASVspoof 2019 dataset rather than the entire corpus. 
- **Future Scaling:** Deploying this training pipeline to dedicated cloud compute instances would allow for processing the full dataset. This broader data exposure is expected to further stabilize the multimodal fusion weights and potentially push the zero-shot generalization accuracy even higher.
- **Model Quantization:** Future work would involve quantizing the HuBERT and DistilBERT models to reduce the memory footprint for edge-device deployment.
  
