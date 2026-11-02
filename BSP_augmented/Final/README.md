# tests

This folder collects the model comparisons and the two newer boundary experiments for the project. The goal is to segment rooftop solar panels in Bengaluru aerial images, where roof styles, shadows, trees, and dense housing make the task harder than on the benchmark and crowdsourced images.

## The five baseline models

These models were trained on the augmented Bengaluru training split and evaluated on its held-out test split. The reported split contains 160 training images (including augmented versions), 10 validation images, and 13 test images. Scores below are transcribed from the baseline-results screenshot in this folder.

| Baseline model | Test IoU | Test mIoU | Test Dice | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|---:|---:|
| MultiResUNet | 0.8658 | 0.9154 | 0.9151 | 0.9194 | 0.9011 | 0.9151 |
| Residual U-Net | 0.8591 | 0.9262 | 0.9242 | **0.9605** | 0.8905 | 0.9242 |
| U-Net | 0.8493 | 0.9012 | 0.8912 | 0.9012 | 0.8823 | 0.8912 |
| U-Net++ | 0.8882 | 0.9313 | 0.9308 | 0.9022 | 0.9302 | 0.9308 |
| ResNet-UNet | **0.8890** | **0.9317** | **0.9312** | **0.9026** | **0.9317** | **0.9317** |


## Why add boundary prediction?

The baseline model predicts the panel mask directly. A small auxiliary boundary head gives it an additional training signal for panel edges, which may help where roofs and panels have similar appearance. Boundary-guided attention takes the next step and uses the predicted edge map to reweight decoder features before producing the final mask.

| New model | Test IoU | Test Dice | Boundary-head F1 (2 px tolerance) | Mean absolute fragment-count error |
|---|---:|---:|---:|---:|
| ResNet34-U-Net + auxiliary boundary head | **0.9012** | **0.9480** | **0.9540** | **0.23** |
| ResNet34-U-Net + boundary-guided attention | 0.8937 | 0.9439 | 0.9046 | 1.31 |

The boundary-head experiment currently has the stronger scores of these two runs. Boundary-guided attention also produced a fragmented prediction on one test image. These are single runs on a 13-image test set, so treat the differences as early evidence rather than a final statistical conclusion. The boundary-head and boundary-guided notebooks use the same training setup, making their comparison more direct than a comparison with the older baseline runs.
