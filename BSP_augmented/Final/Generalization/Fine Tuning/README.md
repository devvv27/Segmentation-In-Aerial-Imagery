# Fine Tuning: adapting Google-trained models to Bengaluru

This folder fine-tunes the six Google-trained checkpoints (see `README_Results.md`) on the 20 Bengaluru training images. As a reference, it also trains an ImageNet-initialised ResNet34-UNet on the same 20 images.

| Notebook | What it is |
|---|---|
| `BSP_FineTune_20.ipynb` | Notebook source |
| `bsp-fewshot-finetuning.ipynb` | Executed Kaggle run of `BSP_FineTune_20.ipynb` |
| `BSP_FewShot_FineTuning.ipynb` | Source for a larger grid: k = 5, 10 or 20 images, with and without augmentation, Google or ImageNet starting weights. **Not run yet.** |

Settings: batch size 5, AdamW with learning rate 2e-4 and weight decay 1e-2, with early stopping on the validation set. The boundary models also train their boundary head with weight 0.2.

## Seen data (used in training)

- **Pretraining:** the crowdsourced Google dataset (8,868 training and 999 validation images). The reference model is pretrained on ImageNet instead.
- **Fine-tuning:** the 20 Bengaluru training images. These are Haralur 7, HSR Layout 6 and Krystal Layout 7.
- **Validation:** the 10 Bengaluru validation images (Haralur 7, HSR Layout 1 and Krystal Layout 2). These were used only for early stopping and for picking the best checkpoint.

## Unseen data (used only for testing)

- **The 13 Bengaluru test images:** Haralur 2, HSR Layout 6 and Krystal Layout 5.
- These are the same 13 images used in the zero-shot evaluation, so before and after can be compared directly.

## 1. Before vs after fine-tuning (unseen test 13)

| Model | IoU before (zero-shot) | IoU after | Gain | F1 before | F1 after |
|---|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.3559 | 0.8365 | 0.4806 | 0.5250 | 0.9110 |
| + Axial attention | 0.4186 | 0.8452 | 0.4266 | 0.5902 | 0.9161 |
| + Axial + Transformer | 0.3593 | 0.8711 | 0.5118 | 0.5287 | 0.9311 |
| + Boundary head | 0.2227 | **0.9029** | **0.6802** | 0.3643 | **0.9490** |
| + Boundary-guided attention | 0.4217 | 0.8896 | 0.4679 | 0.5933 | 0.9416 |
| + StyleGate | 0.2552 | 0.8325 | 0.5774 | 0.4066 | 0.9086 |
| ResNet34-UNet, ImageNet init (no Google pretraining) | n/a | 0.8874 | n/a | n/a | 0.9403 |

## 2. Full test metrics after fine-tuning (unseen test 13)

| Model | IoU | mIoU | Dice | Precision | Recall |
|---|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.8365 | 0.9142 | 0.9110 | 0.9111 | 0.9108 |
| + Axial attention | 0.8452 | 0.9188 | 0.9161 | 0.9241 | 0.9082 |
| + Axial + Transformer | 0.8711 | 0.9323 | 0.9311 | 0.9110 | 0.9521 |
| + Boundary head | **0.9029** | **0.9491** | **0.9490** | **0.9427** | 0.9554 |
| + Boundary-guided attention | 0.8896 | 0.9421 | 0.9416 | 0.9267 | 0.9569 |
| + StyleGate | 0.8325 | 0.9118 | 0.9086 | 0.8568 | **0.9671** |
| ResNet34-UNet, ImageNet init | 0.8874 | 0.9409 | 0.9403 | 0.9157 | 0.9664 |

## 3. IoU by layout after fine-tuning

| Model | Haralur (2 test) | HSR (6 test) | Krystal (5 test) | Validation IoU (10) |
|---|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.8194 | 0.8385 | 0.8405 | 0.8313 |
| + Axial attention | 0.8610 | 0.8151 | 0.8601 | 0.8222 |
| + Axial + Transformer | 0.8273 | 0.8326 | 0.9096 | 0.8057 |
| + Boundary head | 0.8507 | **0.8672** | 0.9413 | 0.8254 |
| + Boundary-guided attention | 0.8473 | 0.8434 | 0.9318 | **0.8804** |
| + StyleGate | **0.9129** | 0.7248 | 0.8891 | 0.7766 |
| ResNet34-UNet, ImageNet init | 0.8666 | 0.8141 | **0.9422** | 0.7873 |

The Haralur column rests on only 2 test images, so treat it with care.

## Takeaways

- Fine-tuning on just 20 local images lifts every Google-trained model from 0.22 to 0.42 IoU up to 0.83 to 0.90 IoU.
- The final scores are close together and the validation and test rankings disagree. With only 10 validation and 13 test images, the differences between models after fine-tuning are within noise.
