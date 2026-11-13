# Zero Shot: Google-trained models tested on Bengaluru

This folder tests the six models trained only on Google images (see `README_Results.md`) on Bengaluru rooftops, without any retraining.

| Notebook | What it is |
|---|---|
| `BSP_ZeroShot_Evaluation.ipynb` | Notebook source |
| `bsp-zeroshot-evaluation.ipynb` | Executed Kaggle run with outputs |

## Seen data (used in training)

- **Only the crowdsourced Google dataset:** 8,868 training images and 999 validation images.
- **No Bengaluru images or labels** were used to train or tune any model.

## Unseen data (used only for testing)

- **All 43 Bengaluru images:** Haralur 16, HSR Layout 13 and Krystal Layout 14. The 13-image test split is also reported on its own.
- **The augmented Bengaluru set:** 183 images, used as a check.
- **AdaBN** re-estimates the BatchNorm statistics on the Bengaluru images being evaluated. It uses the images only, never the masks, and no weights change.

## 1. Google test vs Bengaluru (zero-shot IoU)

| Model | Google test (seen domain) | Bengaluru, all 43 | Bengaluru, test 13 | Drop (Google minus all 43) |
|---|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.852 | 0.300 | 0.356 | 0.552 |
| + Axial attention | 0.853 | **0.350** | 0.419 | **0.503** |
| + Axial + Transformer | 0.847 | 0.300 | 0.359 | 0.547 |
| + Boundary head | 0.853 | 0.178 | 0.223 | 0.675 |
| + Boundary-guided attention | 0.850 | 0.320 | **0.422** | 0.530 |
| + StyleGate | 0.847 | 0.185 | 0.255 | 0.662 |

All six models lose more than half of their IoU on Bengaluru.

## 2. All 43 Bengaluru images in detail

| Model | IoU | mIoU | Dice | Precision | Recall |
|---|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.300 | 0.635 | 0.461 | 0.650 | 0.358 |
| + Axial attention | **0.350** | **0.661** | **0.518** | 0.720 | **0.405** |
| + Axial + Transformer | 0.300 | 0.635 | 0.462 | 0.662 | 0.355 |
| + Boundary head | 0.178 | 0.570 | 0.302 | 0.488 | 0.219 |
| + Boundary-guided attention | 0.320 | 0.646 | 0.485 | **0.732** | 0.363 |
| + StyleGate | 0.185 | 0.575 | 0.312 | 0.538 | 0.220 |

Recall is low for every model, so most panels are missed. Precision is higher than recall for every model.

## 3. IoU by layout (all 43 images)

| Model | Haralur (16) | HSR (13) | Krystal (14) |
|---|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.142 | 0.409 | 0.421 |
| + Axial attention | **0.198** | **0.470** | **0.459** |
| + Axial + Transformer | 0.169 | 0.390 | 0.404 |
| + Boundary head | 0.084 | 0.286 | 0.219 |
| + Boundary-guided attention | 0.135 | 0.465 | 0.456 |
| + StyleGate | 0.031 | 0.359 | 0.256 |

Haralur is the hardest layout for every model.

## 4. Label-free test-time methods (IoU on all 43 images)

| Model | Zero-shot | TTA | AdaBN | AdaBN + TTA |
|---|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.300 | 0.265 | 0.320 | 0.318 |
| + Axial attention | **0.350** | **0.359** | 0.348 | **0.357** |
| + Axial + Transformer | 0.300 | 0.269 | 0.254 | 0.244 |
| + Boundary head | 0.178 | 0.188 | 0.298 | 0.306 |
| + Boundary-guided attention | 0.320 | 0.295 | **0.357** | 0.341 |
| + StyleGate | 0.185 | 0.162 | 0.230 | 0.268 |

- **TTA** averages predictions over 8 flipped and rotated views of each image.
- **AdaBN** re-estimates BatchNorm statistics on the Bengaluru images.

AdaBN helps most models, most of all Boundary head (0.178 to 0.298). TTA alone usually lowers IoU.

## 5. Input-scale sweep (IoU on all 43 images, diagnostic only)

| Model | ×0.5 | ×0.75 | ×1.0 | ×1.25 | ×1.5 | ×2.0 |
|---|---:|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.099 | 0.261 | **0.300** | 0.293 | 0.258 | 0.170 |
| + Axial attention | 0.301 | **0.448** | 0.350 | 0.284 | 0.194 | 0.074 |
| + Axial + Transformer | 0.179 | 0.280 | 0.300 | **0.310** | 0.305 | 0.185 |
| + Boundary head | 0.043 | 0.163 | 0.178 | **0.207** | 0.151 | 0.069 |
| + Boundary-guided attention | 0.157 | 0.305 | **0.320** | 0.289 | 0.233 | 0.126 |
| + StyleGate | 0.072 | 0.185 | 0.185 | **0.191** | 0.139 | 0.082 |

Bold marks each model's best scale. There is no common best scale across the models.

## 6. Panel-level errors (all 43 images)

| Model | Recall, small panels | Recall, medium panels | Recall, large panels | False-positive blobs per image | Predicted / true panel area |
|---|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.17 | 0.53 | 0.35 | 1.16 | 0.55 |
| + Axial attention | 0.07 | 0.53 | 0.45 | 1.12 | 0.56 |
| + Axial + Transformer | 0.10 | 0.43 | 0.39 | 0.98 | 0.54 |
| + Boundary head | 0.07 | 0.20 | 0.26 | 1.09 | 0.45 |
| + Boundary-guided attention | 0.13 | 0.50 | 0.39 | 0.63 | 0.50 |
| + StyleGate | 0.13 | 0.33 | 0.23 | 1.42 | 0.41 |

Every model predicts only about half of the true panel area, and small panels are missed the most.

## 7. Google vs Bengaluru image statistics

| Dataset | Panel regions per image | Median region area (px) | Panel fraction | Brightness | Contrast | Saturation |
|---|---:|---:|---:|---:|---:|---:|
| Google test (999) | 1.25 | 740 | 1.4% | 96 | 48 | 56 |
| Bengaluru (43) | 2.12 | 855 | 3.7% | 147 | 49 | 38 |

The median panel area is similar in the two datasets. Bengaluru images are much brighter and less saturated, so the gap is mainly in how the images look rather than in panel size.
