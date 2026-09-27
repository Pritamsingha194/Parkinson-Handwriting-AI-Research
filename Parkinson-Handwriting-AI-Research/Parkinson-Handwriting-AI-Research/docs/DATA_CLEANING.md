# Dataset Cleaning and Preparation

## Why cleaning became necessary

The project encountered multiple data-quality and reproducibility issues:

- exact duplicate images
- near-duplicate handwriting images
- class imbalance
- different folder layouts between dataset versions
- path variables pointing at unintended directories
- notebook variables being overwritten between experiments
- accidental training on a subset instead of the canonical merged dataset

## Cleaning pipeline

```text
Raw dataset
   ↓
Validate root folders
   ↓
Recursive image collection
   ↓
Readability check
   ↓
Label assignment
   ↓
Exact hash / pHash
   ↓
Remove exact duplicates
   ↓
Remove near-duplicates
   ↓
Clean manifest
   ↓
Stratified split
   ↓
Train augmentation only
```

## NewHandPD documented run

```text
Raw:          512
After pHash:  467
Final clean:  359
Healthy:      185
Parkinson:    174
```

The project presentation separately records a 512→369 cleaning result. These are **different experimental cleaning runs**, so they must not be silently merged into one claim.

## Current engineering rule

The canonical pipeline must generate a manifest such as:

```text
image_path | dataset | task | label | phash | split
```

Once generated, this manifest is frozen for a model experiment.

## Split policy

The research direction uses stratified train/validation/test splitting. No validation/test augmentation is permitted.

## Class imbalance

Class imbalance is handled through controlled class weighting and/or training-set augmentation/oversampling. The exact strategy must be recorded per experiment rather than changed silently.
