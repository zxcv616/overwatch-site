# Evaluation protocol

[← Project overview](../README.md)

## Keep development separate from the final comparison

- **Train:** 6,471 images for learning model weights.
- **Validation:** 548 images for development and subsequent error analysis.
- **Test-dev:** 1,610 labeled images for one frozen final comparison.

The final scheduled epoch-50 checkpoint was selected before inspecting test-dev results. Both final models ran on the same Colab CPU runtime with identical inference settings. Training used a Colab T4 GPU. Final-test inference was not repeated to generate the gallery.

## Compare a shared label space

| Shared category | VisDrone | COCO |
| :--- | :--- | :--- |
| Person | Pedestrian + people | Person |
| Bicycle | Bicycle | Bicycle |
| Car | Car | Car |
| Truck | Truck | Truck |
| Bus | Bus | Bus |
| Motorcycle | Motor | Motorcycle |

Van, tricycle, and awning-tricycle have no clean baseline match. They are non-scored regions in the shared comparison. The fine-tuned ten-class results include them and are a separate task.

Both models apply native class-aware NMS before mapping. No second NMS is applied after pedestrian/people are merged; retained duplicates are scored normally.

## Scoring

- pycocotools with explicit COCO-style ground truth.
- 320px inference; confidence cutoff 0.001; NMS IoU 0.7.
- FP32, batch size 1, no inference augmentation, rectangular batching disabled.
- Detector cap: 3,000 predictions per image. Scorer cap: 3,000 per image per category. Neither truncated the final results.
- Object sizes follow COCO area ranges using original-resolution box area.
- Ignored regions, others, score-zero annotations, and shared-space exclusions become crowd regions in each evaluated category. Valid matches take precedence; crowd overlap may ignore detections. This is a documented approximation of VisDrone ignore behavior.

## Read the metrics correctly

**mAP@0.5** measures average precision with a bounding-box overlap threshold of 0.5. **mAP@0.5:0.95** averages across stricter overlap thresholds. Both summarize a precision–recall curve; neither is classification accuracy.

The gallery uses a **0.25 display confidence cutoff** for both models and **IoU 0.5** matching. Its precision/recall values describe that operating point on validation, not final-test AP. Confidence values are not guaranteed to be equally calibrated between models.

Confusion matrices use localization-first matching after collapsing classes, then compare original class labels. This differs from the category-aware matching used for gallery counts. See the [analysis report](../dist/assets/m6-README.md) for details.

## Evidence and reproducibility scope

The repository includes exported metric tables, evaluation settings, per-case counts, selected predictions, and the [validation provenance record](../dist/assets/m6-analysis.json). That record identifies model and prediction hashes.

The original experiment recorded environment locks, raw predictions, checkpoints, and source hashes. The main experimental workspace was unversioned; the website’s Git commits must not be presented as the training-code revision. Training scripts, the full dataset, checkpoints, and the complete run environment are not in this repository, so it is not a standalone training reproduction package.

## Limits

One seed does not establish statistical significance. Sequence similarity within VisDrone limits claims beyond this dataset. Small objects remain difficult, and selected gallery examples intentionally include extremes. Runtime benchmarks and deployment on drone hardware have not been performed. A new confirmatory experiment needs a fresh holdout or an explicitly disclosed follow-up protocol.
