# Zero Shot: Google-trained models tested on Bengaluru

This is the same setup as Experiment 2. The models are trained only on the crowdsourced Google dataset and evaluated on the Bengaluru solar panels test set without any retraining, to measure how well they generalise.

| Notebook | What it is |
|---|---|
| `BSP_ZeroShot_Evaluation.ipynb` | Notebook source |
| `bsp-zeroshot-evaluation.ipynb` | Executed Kaggle run with outputs |

## Seen data (used in training)

- **Only the crowdsourced Google dataset:** 8,868 training images and 999 validation images.
- **No Bengaluru images or labels** were used.

## Unseen data (used only for testing)

- **The 13 Bengaluru test images:** Haralur 2, HSR Layout 6 and Krystal Layout 5.

## Results on the Bengaluru test set (13 images, no retraining)

| Model | IoU | mIoU | Dice | Precision | Recall | F1-Score |
|---|---:|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.3559 | 0.6616 | 0.5250 | 0.7483 | 0.4043 | 0.5250 |
| + Axial attention | 0.4186 | 0.6952 | 0.5902 | 0.8416 | **0.4544** | 0.5902 |
| + Axial + Transformer | 0.3593 | 0.6642 | 0.5287 | 0.8190 | 0.3903 | 0.5287 |
| + Boundary head | 0.2227 | 0.5914 | 0.3643 | 0.6188 | 0.2582 | 0.3643 |
| + Boundary-guided attention | **0.4217** | **0.6969** | **0.5933** | **0.8560** | 0.4539 | **0.5933** |
| + StyleGate | 0.2552 | 0.6093 | 0.4066 | 0.7295 | 0.2819 | 0.4066 |

The baseline row is the same model as ResNet-UNet in Experiment 2 (IoU 0.3559). Recall is low for every model, so most panels are missed.
