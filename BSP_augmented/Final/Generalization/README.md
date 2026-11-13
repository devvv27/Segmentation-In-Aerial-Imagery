# Generalization: do better architectures transfer better?

## Why we did this

On our Bengaluru dataset (BSP), adding attention and transformer modules to ResNet-UNet barely changed the scores. The baseline was already at about 0.89 IoU there, so there wasn't much room left to improve.

So we asked a different question. An improved architecture may not raise the numbers when a model is trained and tested on the same kind of data. It could still help when the model has to deal with images it has never seen. To test this, we trained every version of ResNet-UNet only on the crowdsourced Google dataset and then checked how well it worked on Bengaluru.

## What is in this folder

| Folder | Step | What happens |
|---|---|---|
| `Results/` | 1. Train | The baseline ResNet-UNet and five improved versions are trained on the crowdsourced Google dataset. |
| `Zero Shot/` | 2. Test on Bengaluru | The same models are tested on Bengaluru images without any retraining. |
| `Fine Tuning/` | 3. Adapt | The same models are fine-tuned on 20 Bengaluru images and tested again. |

The `CD_ResnetUnet_*.ipynb` notebooks in this folder are the training notebooks for step 1. Their executed runs are in `Results/`.

## The models

| Model | What was added to ResNet-UNet |
|---|---|
| Baseline | Nothing (ResNet34 encoder and U-Net decoder) |
| Axial attention | Row and column attention on the skip connections |
| Axial + Transformer | The same axial attention plus a small Transformer at the bottleneck |
| Boundary head | An extra output that learns panel edges |
| Boundary-guided attention | The boundary head, with its edge map used to reweight the decoder |
| StyleGate | A module aimed at colour and texture differences between images |

## Seen and unseen data

- **Seen in training:** 8,868 Google training images and 999 Google validation images. In step 3 the models also see 20 Bengaluru training images. 10 Bengaluru validation images are used to pick the checkpoint.
- **Never seen:** 999 Google test images and the 13 Bengaluru test images (Haralur 2, HSR Layout 6, Krystal Layout 5).

## Main results (IoU)

| Model | Google test (999) | Bengaluru, zero-shot (13 test) | Bengaluru, after fine-tuning (13 test) |
|---|---:|---:|---:|
| Baseline | 0.8518 | 0.3559 | 0.8365 |
| Axial attention | 0.8526 | 0.4186 | 0.8452 |
| Axial + Transformer | 0.8472 | 0.3593 | 0.8711 |
| Boundary head | **0.8530** | 0.2227 | **0.9029** |
| Boundary-guided attention | 0.8498 | **0.4217** | 0.8896 |
| StyleGate | 0.8466 | 0.2552 | 0.8325 |

## What this shows

- **On the Google data the models are almost identical.** All six are within 0.007 IoU of each other, so the extra modules make no real difference on the data the models were trained on. This matches what we saw on BSP.
- **On unseen Bengaluru images some of them clearly help.** Axial attention and boundary-guided attention score about 0.06 IoU higher than the baseline without any retraining. Axial attention is the clearest case: the same score as the baseline on Google, but noticeably better on Bengaluru.
- **Not every change helps.** The Transformer version is about the same as the baseline, while the boundary head and StyleGate do worse than the baseline on Bengaluru before fine-tuning.
- **Fine-tuning on 20 Bengaluru images closes most of the gap for all of them.** Every model ends up between 0.83 and 0.90 IoU, with the boundary head highest at 0.9029.

The full tables are in the README of each folder.
