# Research Timeline

## 1. Baseline: handwriting classification

The project began with handwriting images from HandPD, using Spiral and Meander tasks. The early pipeline resized images and normalized pixel values before training image classifiers.

A historical experiment contained 808 images with 216 Healthy and 592 Parkinson samples. This immediately made class imbalance a major modelling consideration.

## 2. Transfer learning

VGG16 and ResNet50 were tested as baseline feature extractors. These experiments provided reference points before designing a task-specific architecture.

## 3. Custom CNN / FCNN

A custom CNN was developed to learn handwriting-specific local stroke patterns. Progressive filter reduction was used to compress representations, after which extracted features were passed to a fully connected classifier.

Historical result: **84.56% overall accuracy**.

This became an important baseline, but the experiment also exposed the need to investigate dataset quality, imbalance and generalization more deeply.

## 4. Data-quality investigation

The research then shifted from “try another model” to “make the dataset trustworthy.” Exact duplicates, near-duplicates, folder/path inconsistencies and changing notebook variables were identified as major sources of experimental instability.

A pHash cleaning pipeline was introduced.

## 5. NewHandPD cleaning

NewHandPD was collected recursively, class-labelled and cleaned. A documented run reduced 512 images to 369 clean images after duplicate/near-duplicate processing.

## 6. Merge and standardize

HandPD and NewHandPD were separately cleaned and then merged for controlled experiments. A current merged clean state contained 916 images: 304 Healthy and 612 Parkinson.

The important engineering decision was to **freeze the dataset state before model training**.

## 7. Transformer motivation

The project moved toward Transformers because handwriting contains both local stroke irregularities and broader spatial relationships. Patch-based self-attention offered a way to investigate these relationships without depending entirely on convolutional feature extraction.

## 8. Custom ViT experiment

A pretrained ViT backbone was combined with the project's progressive representation idea:

`526 → 256 → 128 → 64 → 32 → 16 → 8 → 4 → 2 → 1`

This was a useful research step but still relied on a pretrained ViT backbone.

## 9. Decision: ViT from scratch

The next research question became:

> Can the progressive architecture idea be implemented with a genuine Transformer trained from scratch, without a CNN backbone and without pretrained ViT weights?

This led to the current Mini-ViT direction.

## 10. Current debugging stage

Early from-scratch Mini-ViT runs showed unstable validation accuracy, including oscillation around two class-proportion-like values. A major investigation point was discovered: some runs were accidentally using smaller subsets such as 279 training / 93 test images rather than the intended merged clean dataset.

Therefore the next stage is not to blindly increase epochs. The dataset manifest, split, labels, class weights, augmentation and model inputs must first be frozen and verified.
