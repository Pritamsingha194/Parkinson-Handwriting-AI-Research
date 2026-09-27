# Parkinson's Disease Detection from Handwriting Images

A reproducible research repository documenting the development of an AI-based Parkinson's Disease (PD) detection system from handwriting images.

> **Research focus:** dataset cleaning → classical/transfer-learning baselines → custom CNN/FCNN → transformer/ViT experiments → a from-scratch Mini-ViT direction.

## Why this repository exists

This repository is intentionally more than a final-model repository. It records **how the research evolved**, including dataset problems, failed experiments, fixes, architecture changes, and the reasoning behind moving from one approach to the next.

The original academic project was titled **“Hybrid DL and ML for Automated Parkinson’s Disease Detection.”** The documented work includes preprocessing, transfer-learning experiments, a custom CNN/FCNN pipeline, and model evaluation. fileciteturn1file0L60-L74

## Research ownership / technical contribution

**Primary technical implementation and experimentation:** Pritam Singha

The project involved academic collaboration and guidance, while Pritam led the practical ML/DL pipeline development, experimentation, debugging, dataset preparation, architecture iteration, and reproducibility work documented here.

## Research evolution

### Phase 1 — Initial handwriting pipeline

The first pipeline worked with the original HandPD handwriting material, particularly Spiral and Meander drawings. Early experiments used 128×128 images, normalization, augmentation and class weighting.

A historical implementation loaded 808 images with a strong class imbalance (216 Healthy / 592 Parkinson), followed by a stratified train/test split. fileciteturn1file2L15-L43

### Phase 2 — Transfer-learning baselines

VGG16 and ResNet50 were investigated as baseline feature extractors before moving toward custom architectures. These experiments established the feasibility of image-based PD classification but also exposed the limitations of relying on generic pretrained representations for this small, imbalanced handwriting dataset.

### Phase 3 — Custom CNN + FCNN

A custom CNN was designed specifically for the handwriting problem. The filter hierarchy was progressively reduced to learn increasingly compact representations. One documented implementation used:

`256 → 128 → 64 → 32 → 8 → 4 → 2`

with a dedicated feature layer, followed by dense classification. fileciteturn1file4L66-L98

The broader research architecture also explored the deeper conceptual hierarchy:

`512 → 256 → 128 → 64 → 32 → 16 → 8 → 4 → 2`

The earlier CNN/FCNN research achieved a documented **84.56% overall accuracy**. That result is retained as a historical baseline and is not presented as the result of the current ViT work. fileciteturn1file3L110-L116

### Phase 4 — Dataset-quality problem becomes central

As experiments progressed, it became clear that model architecture alone was not enough. The dataset contained exact duplicates, near-duplicates, inconsistent folder structures/path assumptions, class imbalance, and notebook-state/variable-overwrite problems.

This led to a dedicated data-cleaning pipeline using perceptual hashing (pHash), followed by leakage-aware train/validation/test preparation.

The research presentation documents NewHandPD being reduced from **512 collected images to 369 clean images** after near-duplicate removal, followed by an 80–10–10 stratified split and 224×224 grayscale preprocessing. fileciteturn1file3L119-L125

### Phase 5 — Transformer / ViT direction

After the CNN work, the project moved toward Transformer-based modelling because the research goal was to capture handwriting patterns beyond purely local convolutional features.

The proposed methodology uses patch embedding, multi-head self-attention, progressive dimensional reduction, regularization/hyperparameter tuning, and evaluation through accuracy, AUC, F1-score and confusion matrices. fileciteturn1file3L128-L133

### Phase 6 — Custom ViT experiments

An earlier Custom ViT implementation used a pretrained ViT backbone and then a progressive dense representation:

`526 → 256 → 128 → 64 → 32 → 16 → 8 → 4 → 2 → 1`

The archived notebook shows the pretrained ViT backbone and the 526-to-1 progressive head. fileciteturn1file2L104-L122 fileciteturn1file2L136-L151

This experiment was valuable, but it did not fully satisfy the research direction of a **true ViT built from scratch**.

### Phase 7 — From-scratch Mini-ViT

The current direction therefore removes CNN feature extraction and pretrained ViT weights entirely.

The intended architecture is:

`Input image → Patch Embedding → Positional Embedding → Transformer Block(s) → Progressive Transformer/MLP Representation → Binary Classifier`

The progressive dimensional idea remains inspired by the earlier custom architecture, but the feature-learning mechanism is Transformer self-attention rather than convolution.

**Current status:** this stage is still under development. The recent unstable Mini-ViT runs are treated as debugging/ablation evidence, not as final performance.

## Dataset strategy

### Datasets

- **HandPD:** original dataset containing Spiral and Meander handwriting tasks.
- **NewHandPD:** extended dataset containing Spiral, Meander and Circle tasks.

The research material records HandPD as 92 participants / 736 images and NewHandPD as 66 participants / 264 images in the referenced dataset description. fileciteturn1file3L76-L107

### Current cleaning philosophy

1. Identify the correct dataset roots.
2. Recursively collect images from Healthy/Parkinson folders.
3. Verify image readability.
4. Compute perceptual hashes.
5. Remove exact duplicates.
6. Remove near-duplicates.
7. Freeze the resulting clean path/label lists.
8. Perform stratified splitting before augmentation.
9. Keep validation/test images untouched by augmentation.
10. Record class counts at every stage.

This was introduced specifically to reduce leakage risk and prevent inconsistent notebook states from silently changing the training set.

## Important historical dataset states

| State | Images | Healthy | Parkinson | Purpose |
|---|---:|---:|---:|---|
| Early HandPD + Meander raw experiment | 808 | 216 | 592 | Early baseline experiments |
| NewHandPD raw/current cleaning run | 512 | 233 | 279 | Before pHash cleaning |
| NewHandPD clean run | 359 | 185 | 174 | Cleaned NewHandPD experiment |
| Current merged clean experiment | 916 | 304 | 612 | HandPD + NewHandPD merged pipeline |
| Earlier combined/raw experiment | 1320 | 449 | 871 | Historical experiment; **not canonical** |

Counts are preserved as experiment history. They must not be mixed across experiments.

## What went wrong — and what we changed

| Problem | Consequence | Solution |
|---|---|---|
| Class imbalance | Model bias toward Parkinson class | Class weights + controlled augmentation/oversampling |
| Exact duplicates | Inflated effective dataset size | pHash/exact-hash cleaning |
| Near-duplicates | Possible leakage and overly optimistic validation | Perceptual-hash similarity filtering |
| Inconsistent folder structures | Missing/incorrect image collection | Explicit root validation + recursive collection |
| Notebook variables overwritten | Training on unintended subsets | Stage-locked dataset variables |
| Small validation subsets | Highly unstable validation accuracy | Fixed stratified split and frozen datasets |
| Augmentation applied incorrectly | Leakage risk | Augment training only |
| Pretrained ViT did not meet research objective | Architecture not fully from scratch | Move to custom Mini-ViT |
| Mini-ViT unstable training | Accuracy oscillation / class collapse | Debug data identity, labels, loss, attention pipeline, class weighting and regularization before claiming results |

## Current canonical research rule

**Never report a model result without recording:**

- exact dataset version
- exact class counts
- split seed
- train/validation/test counts
- preprocessing
- augmentation policy
- class weights
- model architecture
- optimizer and learning rate
- number of epochs
- best checkpoint
- accuracy, precision, recall, F1 and AUC
- confusion matrix

## Repository structure

```text
Parkinson-Handwriting-AI-Research/
├── README.md
├── .gitignore
├── LICENSE
├── docs/
│   ├── RESEARCH_TIMELINE.md
│   ├── DATA_CLEANING.md
│   ├── ARCHITECTURE_EVOLUTION.md
│   └── EXPERIMENT_LOG.md
├── src/
│   ├── data/
│   ├── models/
│   ├── training/
│   └── evaluation/
├── configs/
├── experiments/
│   └── notebooks/
├── results/
│   └── figures/
└── references/
```

## Reproducibility policy

The public repository should **not contain the raw medical/handwriting dataset** unless its redistribution license explicitly permits it. Instead, the code should expect a user-provided dataset path and generate a manifest containing paths, labels, hashes and split assignments.

## Medical-AI disclaimer

This is a research/academic project. It is not a clinical diagnostic system and must not be used as a substitute for professional medical evaluation. Any future performance claim requires independent validation and careful subject-level leakage analysis.

## Status

🚧 **Research in progress**

- Historical CNN/FCNN baseline: documented
- Dataset cleaning pipeline: developed
- HandPD + NewHandPD integration: developed across experiments
- Custom pretrained-ViT experiment: documented
- From-scratch Mini-ViT: **under active development**
- Final model: **not yet frozen**
