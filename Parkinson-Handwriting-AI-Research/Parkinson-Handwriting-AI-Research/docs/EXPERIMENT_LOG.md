# Experiment Log

| Experiment | Data state | Architecture | Status | Key lesson |
|---|---|---|---|---|
| Early HandPD/Meander | 808 raw | Transfer/CNN | Historical | Strong class imbalance |
| VGG16 | HandPD/Meander | VGG16 | Historical | Transfer-learning baseline |
| ResNet50 | HandPD/Meander | ResNet50 | Historical | Transfer-learning baseline |
| Custom CNN | HandPD/Meander | Custom CNN | Historical | Task-specific features work |
| CNN + FCNN | HandPD/Meander | CNN feature extraction + FCNN | Historical | 84.56% documented baseline |
| NewHandPD cleaning | 512 raw | pHash cleaning | Completed | Duplicate control is essential |
| HandPD cleaning | HandPD clean runs | pHash cleaning | Completed/iterative | Root/path validation matters |
| Merged clean dataset | 916 current clean | Preprocessing pipeline | Current | Freeze data before modelling |
| Pretrained Custom ViT | Clean/experimental subsets | ViT + progressive head | Historical | Useful but not fully from scratch |
| Mini-ViT | Experimental subsets + merged pipeline | ViT from scratch | In progress | Validation instability must be debugged at pipeline level |

## Current Mini-ViT debugging checklist

Before another long training run:

1. Print the dataset manifest identity.
2. Print exact train/validation/test counts.
3. Print class counts for every split.
4. Confirm labels are 0/1.
5. Confirm image shape is `(224,224,1)`.
6. Confirm no image path occurs in multiple splits.
7. Confirm augmentation is training-only.
8. Print class weights.
9. Run one small batch through the model.
10. Verify logits/probabilities are not constant.
11. Run a tiny overfit test on a small fixed subset.
12. Only then launch the full training run.
