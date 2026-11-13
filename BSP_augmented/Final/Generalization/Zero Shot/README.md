# Zero Shot: Google-trained models tested on Bengaluru

This is the same setup as Experiment 2. The models are trained only on the crowdsourced Google dataset and evaluated on the Bengaluru solar panels test set without any retraining, to measure how well they generalise.

| Notebook | What it is |
|---|---|
| `BSP_ZeroShot_Evaluation.ipynb` | Notebook source |
| `bsp-zeroshot-evaluation.ipynb` | Executed Kaggle run with outputs |

## Seen data (used in training)

- **Only the crowdsourced Google dataset:** 8,868 training images and 999 validation images.
- **No Bengaluru images or labels** were used.

### Results on the crowdsourced Google test set (999 images, same domain as training)

| Model | IoU | mIoU | Dice | Precision | Recall | F1-Score |
|---|---:|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.8518 | 0.9247 | 0.9200 | 0.9225 | **0.9175** | 0.9200 |
| + Axial attention | 0.8526 | 0.9252 | 0.9205 | **0.9339** | 0.9074 | 0.9205 |
| + Axial + Transformer | 0.8472 | 0.9224 | 0.9173 | 0.9289 | 0.9059 | 0.9173 |
| + Boundary head | **0.8530** | **0.9254** | **0.9207** | 0.9250 | 0.9164 | **0.9207** |
| + Boundary-guided attention | 0.8498 | 0.9237 | 0.9188 | 0.9253 | 0.9123 | 0.9188 |
| + StyleGate | 0.8466 | 0.9221 | 0.9169 | 0.9243 | 0.9096 | 0.9169 |

## Unseen data (used only for testing)

- **The 13 Bengaluru test images:** Haralur 2, HSR Layout 6 and Krystal Layout 5.

## Results on the Bengaluru test set (13 images, no retraining)

| Model | IoU | mIoU | Dice | Precision | Recall | F1-Score |
|---|---:|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.2559 | 0.5616 | 0.4250 | 0.4483 | 0.3043 | 0.4250 |
| + Axial attention | 0.4186 | 0.6952 | 0.5902 | 0.8416 | **0.4544** | 0.5902 |
| + Axial + Transformer | 0.3593 | 0.6642 | 0.5287 | 0.8190 | 0.3903 | 0.5287 |
| + Boundary head | 0.2227 | 0.5914 | 0.3643 | 0.6188 | 0.2582 | 0.3643 |
| + Boundary-guided attention | **0.4217** | **0.6969** | **0.5933** | **0.8560** | 0.4539 | **0.5933** |
| + StyleGate | 0.2552 | 0.6093 | 0.4066 | 0.7295 | 0.2819 | 0.4066 |


