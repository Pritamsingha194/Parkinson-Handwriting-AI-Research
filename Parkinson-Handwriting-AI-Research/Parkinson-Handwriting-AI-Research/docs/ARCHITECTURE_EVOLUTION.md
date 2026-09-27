# Architecture Evolution

## A. Classical / transfer-learning baselines

### VGG16

Used as a transfer-learning reference for extracting visual representations from handwriting images.

### ResNet50

Used as another transfer-learning reference to compare deeper residual representations against the task-specific architecture.

---

## B. Custom CNN → FCNN

The project then moved to a task-specific CNN.

### Historical filter progression

```text
256 → 128 → 64 → 32 → 8 → 4 → 2
```

A separate research description records the broader conceptual progression:

```text
512 → 256 → 128 → 64 → 32 → 16 → 8 → 4 → 2
```

The objective was hierarchical extraction of progressively compact handwriting/tremor representations.

### Pipeline

```text
Image
  ↓
Conv blocks
  ↓
Feature layer
  ↓
Flatten / feature extraction
  ↓
FCNN
  ↓
Healthy / Parkinson
```

Historical documented performance: 84.56% overall accuracy.

---

## C. Pretrained Custom ViT

The next architecture introduced Vision Transformer processing.

The archived Custom ViT V3 implementation used a ViT-B/16 backbone with pretrained weights and then applied the project's progressive representation head.

```text
224×224 image
      ↓
Pretrained ViT-B/16
      ↓
526
 ↓
256
 ↓
128
 ↓
64
 ↓
32
 ↓
16
 ↓
8
 ↓
4
 ↓
2
 ↓
1 sigmoid
```

This architecture was useful for exploring the Transformer direction but did not satisfy the final research objective of a ViT built entirely from scratch.

---

## D. From-scratch Mini-ViT — current direction

The current architecture removes CNNs and pretrained ViT weights.

```text
224×224×1
   ↓
Patch Embedding
   ↓
Positional Embedding
   ↓
Transformer Block
   ↓
Transformer Block
   ↓
Progressive representation
   ↓
Dense/MLP classification head
   ↓
Sigmoid
```

### Core principles

- No Conv2D feature extractor
- No VGG/ResNet backbone
- No pretrained ViT weights
- Multi-head self-attention
- Positional information
- Small-dataset regularization
- Class weighting where justified
- Training-only augmentation
- Frozen dataset manifest

The progressive dimensional idea is retained as a design concept rather than replacing attention with CNN operations.
