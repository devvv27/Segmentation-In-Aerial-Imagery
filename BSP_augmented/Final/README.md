# tests

This folder has every model comparison we've run for the project so far: the five baseline models, three attention variants on top of ResNet-UNet, and two boundary experiments. The goal is to segment rooftop solar panels in Bengaluru aerial images. Roof styles, shadows, trees and dense housing make this harder than the benchmark and crowdsourced datasets.

Everything below uses the same augmented Bengaluru split: 160 training images (augmented versions included), 10 validation images and 13 test images.

## The five baseline models

These were trained on the augmented Bengaluru training split and evaluated on its held-out test split. Scores are transcribed from the baseline-results screenshot in this folder. The ResNet-UNet row was re-checked against its notebook output.

| Baseline model | Test IoU | Test mIoU | Test Dice | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|---:|---:|
| MultiResUNet | 0.8658 | 0.9154 | 0.9151 | 0.9194 | 0.9011 | 0.9151 |
| Residual U-Net | 0.8591 | 0.9262 | 0.9242 | **0.9605** | 0.8905 | 0.9242 |
| U-Net | 0.8493 | 0.9012 | 0.8912 | 0.9012 | 0.8823 | 0.8912 |
| U-Net++ | 0.8882 | 0.9313 | 0.9308 | 0.9022 | 0.9302 | 0.9308 |
| ResNet-UNet | **0.8890** | **0.9417** | **0.9412** | 0.9126 | **0.9717** | **0.9412** |

ResNet-UNet (ResNet34 encoder) came out on top, so it is the base model for everything that follows.

## Attention modules on top of ResNet-UNet

ResNet-UNet is the best baseline, so we kept it as it is (same encoder, decoder, loss and training setup) and only changed the architecture. We tried three attention setups.

**Axial attention.** Every pixel looks along its whole row, then its whole column. We put this on the skip connections (the 1/4, 1/8 and 1/16 resolution features) before they go into the decoder. Solar panels sit in rectangular arrays, so seeing a long straight stretch of context should help with ragged edges and with telling panels from clutter like water tanks and parapets. It is also much cheaper than full self-attention. The new layers start switched off, so training begins from the plain ResNet-UNet.

**Axial + Transformer.** The same axial skips, plus a small two-layer Transformer on the lowest-resolution features (8×8 at our 256×256 input). The idea was to give the model one global look at the whole tile, for example to connect a panel that a shadow has split in two.

**ECA (Efficient Channel Attention).** A very light channel attention placed after each decoder block. It decides which feature channels matter by running a tiny 1D convolution over the pooled channel values. It adds only 19 parameters in total.

| Model | Test IoU | Test mIoU | Test Dice | Precision | Recall | Parameters |
|---|---:|---:|---:|---:|---:|---:|
| ResNet-UNet (baseline, for reference) | 0.8890 | 0.9417 | 0.9412 | 0.9126 | **0.9717** | 24.45M |
| + ECA in the decoder | 0.8991 | 0.9471 | 0.9469 | 0.9343 | 0.9598 | 24.45M |
| + Axial attention on the skips | **0.9051** | **0.9503** | **0.9502** | **0.9520** | 0.9484 | 25.15M |
| + Axial attention + Transformer bottleneck | 0.8994 | 0.9473 | 0.9471 | 0.9507 | 0.9434 | 26.99M |

All three beat the baseline. Axial attention alone did best, and adding the Transformer on top of it did not help (it was slightly worse and costs about 1.8M more parameters). ECA gets close to the Transformer variant for essentially free.

The baseline tends to over-predict panels: it has the best recall but the most false positives. The attention models trade a little recall for precision. Axial attention cut false-positive pixels roughly in half compared with the baseline (3,476 down to 1,785), at the cost of missing some more panel pixels.

## Why add boundary prediction?

The baseline model predicts the panel mask directly. A small auxiliary boundary head gives it an additional training signal for panel edges, which may help where roofs and panels have similar appearance. Boundary-guided attention takes the next step and uses the predicted edge map to reweight decoder features before producing the final mask.

| New model | Test IoU | Test Dice | Boundary-head F1 (2 px tolerance) | Mean absolute fragment-count error |
|---|---:|---:|---:|---:|
| ResNet34-U-Net + auxiliary boundary head | **0.9012** | **0.9480** | **0.9540** | **0.23** |
| ResNet34-U-Net + boundary-guided attention | 0.8937 | 0.9439 | 0.9046 | 1.31 |

The boundary-head experiment currently has the stronger scores of these two runs. Boundary-guided attention also produced a fragmented prediction on one test image. The boundary-head and boundary-guided notebooks use the same training setup, so their comparison is more direct than a comparison with the older baseline runs.

## How the new models compare

Axial attention (0.9051 IoU) and the auxiliary boundary head (0.9012 IoU) are the two best models so far.


