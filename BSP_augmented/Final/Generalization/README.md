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

## Main results (mIoU and F1 score)

| Model | Google test mIoU | Google test F1 | Bengaluru zero-shot mIoU | Bengaluru zero-shot F1 | Bengaluru fine-tuned mIoU | Bengaluru fine-tuned F1 |
|---|---:|---:|---:|---:|---:|---:|
| Baseline | 0.9247 | 0.9200 | 0.6616 | 0.5250 | 0.9142 | 0.9110 |
| Axial attention | 0.9252 | 0.9205 | 0.6952 | 0.5902 | 0.9188 | 0.9161 |
| Axial + Transformer | 0.9224 | 0.9173 | 0.6642 | 0.5287 | 0.9323 | 0.9311 |
| Boundary head | **0.9254** | **0.9207** | 0.5914 | 0.3643 | **0.9491** | **0.9490** |
| Boundary-guided attention | 0.9237 | 0.9188 | **0.6969** | **0.5933** | 0.9421 | 0.9416 |
| StyleGate | 0.9221 | 0.9169 | 0.6093 | 0.4066 | 0.9118 | 0.9086 |
| ImageNet-only reference (no Google training) | n/a | n/a | n/a | n/a | 0.9409 | 0.9403 |

Google results are on the 999 Google test images. All Bengaluru results are on the same 13 Bengaluru test images.

## What this shows

**On the Google data the models are almost identical.** All six are between 0.9221 and 0.9254 mIoU. The extra modules make no real difference on the kind of data the models were trained on, which matches what we saw on BSP.

**Without retraining, some of the improved models clearly generalise better.** Axial attention and boundary-guided attention reach about 0.70 mIoU and 0.59 F1 on Bengaluru, compared with 0.66 and 0.53 for the baseline. Axial attention is the clearest case: it scores the same as the baseline on Google but noticeably better on Bengaluru. Both models are also more precise (0.84 and 0.86 against 0.75) and find more of the panels.

**Not every change helps.** The Transformer version stays at the baseline's level. The boundary head and StyleGate transfer worse than the baseline before fine-tuning.

**Fine-tuning on just 20 Bengaluru images closes most of the gap.** Every model jumps from 0.59 to 0.70 mIoU up to 0.91 to 0.95 mIoU. The boundary head, which transferred worst at first, ends up best with 0.9491 mIoU and 0.9490 F1, followed by boundary-guided attention (0.9421 and 0.9416) and the Transformer version (0.9323 and 0.9311).

**Google pretraining works best together with a better architecture.** The ImageNet-only reference reaches 0.9409 mIoU. That is higher than the fine-tuned plain baseline (0.9142), so Google pretraining alone doesn't help the plain ResNet-UNet. The two boundary models, however, finish above the reference.

These are single runs on 13 test images, so small differences between the fine-tuned models should be read with care. The full tables are in the README of each folder.
