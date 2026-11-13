# Results: training on the crowdsourced Google dataset

These are the executed Kaggle runs of the `CD_ResnetUnet_*.ipynb` notebooks. Each one trains a ResNet34-UNet variant on the crowdsourced Google Earth dataset. The saved checkpoints are later used in the Zero Shot and Fine Tuning folders. The plain ResNet34-UNet baseline was trained the same way in `CrowdSourcedDatasetResults/CD_UnetResnet.ipynb`.

## Seen data (used in training)

- **Training:** 8,868 Google Earth images. These are 2,956 originals, each with two augmented copies (brightness, contrast and blur).
- **Validation:** 999 Google images. These were used only for early stopping and for picking the best checkpoint.

## Unseen data (used only for testing)

- **Test:** 999 Google images.
- **No Bengaluru image** is seen at any point in these runs.

## Comparison on the unseen Google test set (999 images)

| Model | IoU | mIoU | Dice | Precision | Recall | F1 | Epochs run | Parameters |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| ResNet34-UNet (baseline) | 0.8518 | 0.9247 | 0.9200 | 0.9225 | **0.9175** | 0.9200 | 20 | 24.45 M |
| + Axial attention | 0.8526 | 0.9252 | 0.9205 | **0.9339** | 0.9074 | 0.9205 | 16 | 25.15 M |
| + Axial + Transformer | 0.8472 | 0.9224 | 0.9173 | 0.9289 | 0.9059 | 0.9173 | 26 | 26.99 M |
| + Boundary head | **0.8530** | **0.9254** | **0.9207** | 0.9250 | 0.9164 | **0.9207** | 16 | 24.46 M |
| + Boundary-guided attention | 0.8498 | 0.9237 | 0.9188 | 0.9253 | 0.9123 | 0.9188 | 16 | 24.46 M |
| + StyleGate | 0.8466 | 0.9221 | 0.9169 | 0.9243 | 0.9096 | 0.9169 | 17 | 24.46 M |

Bold marks the best value in each column. All training used early stopping with a patience of 10 and a minimum improvement of 1e-4, with a maximum of 200 epochs.

**Takeaway:** all six models score between 0.8466 and 0.8530 IoU. On the domain they were trained on, none of the added modules makes a meaningful difference.
