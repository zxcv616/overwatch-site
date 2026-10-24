# M6 — validation error analysis

M6 reuses the saved M3/M4 predictions on all 548 validation images. No inference or final-test evaluation was repeated. Model and raw-prediction hashes match the recorded runs; shared predictions were regenerated from raw data in memory and verified identical.

## Operating point

Confidence ≥ 0.25 for both models; category-aware COCO matching at IoU ≥ 0.5, all object sizes, with the same per-category crowd ignores as evaluation. This is a fixed display choice, not a calibrated or optimized threshold. Confidence calibration can differ between models. Published AP retains its original 0.001 inference cutoff and is unchanged.

| Validation metric | Pretrained | Fine-tuned |
|---|---:|---:|
| Matched objects | 1,952 | 8,899 |
| False alarms | 564 | 4,271 |
| Missed objects | 33,255 | 26,308 |
| Ignored predictions | 293 | 1,621 |
| Precision | 77.58% | 67.57% |
| Recall | 5.54% | 25.28% |

Fine-tuning increases recall by 19.73 percentage points, but precision falls by 10.01 points at this cutoff. Higher AP does not imply every frame or operating point improves. These validation metrics are separate from M5's final test results.

## Gallery selection

Twelve labeled cases: three original site frames fixed before prediction inspection, then three largest increases in matched objects, three largest decreases, and three largest remaining fine-tuned miss counts. Additional cases have unique filename sequence prefixes; ties use image ID. Selection is sequential in that order. These examples deliberately expose extremes and are not a representative sample. Exact files, counts, and reasons are in `cases.csv`.

The interactive website offers Ground truth / Pretrained / Fine-tuned, class/confidence labels, and ignored-region outlines. `gallery.html` is a portable side-by-side gallery; click its images to enlarge. Green detections match ground truth, orange detections are false alarms, gray detections are ignored. The static ground-truth panels show valid shared objects; dashed ignore outlines are available on the website. Counts are automated diagnostics, not claims about human visual certainty.

## Observations

- **Case 01 — 0000026_00500_d_0000025.jpg:** Parking scene: additional cars are localized, while several small annotated objects remain missed. Matched objects 1 → 11 (+10); false alarms 0 → 2; fine-tuned misses 4/15. 10 newly matched objects and 0 lost matches at this display threshold.
- **Case 02 — 0000242_00627_d_0000003.jpg:** Road scene: nearer vehicles are easier to localize than distant roadside objects; false alarms increase. Matched objects 2 → 9 (+7); false alarms 1 → 6; fine-tuned misses 30/39. 7 newly matched objects and 0 lost matches at this display threshold.
- **Case 03 — 0000194_00800_d_0000123.jpg:** Intersection: additional vehicle matches, with many small pedestrians and road users still missed. Matched objects 2 → 6 (+4); false alarms 2 → 5; fine-tuned misses 36/42. 4 newly matched objects and 0 lost matches at this display threshold.
- **Case 04 — 0000129_02411_d_0000138.jpg:** Dense parking area: a large gain in vehicle matches, accompanied by additional false alarms. Matched objects 0 → 77 (+77); false alarms 0 → 39; fine-tuned misses 89/166. 77 newly matched objects and 0 lost matches at this display threshold.
- **Case 05 — 0000271_06001_d_0000402.jpg:** Shopping street: many more vehicle matches, but dense and distant objects remain missed. Matched objects 9 → 61 (+52); false alarms 2 → 32; fine-tuned misses 90/151. 52 newly matched objects and 0 lost matches at this display threshold.
- **Case 06 — 0000295_02000_d_0000031.jpg:** Crowded junction: fine-tuning finds more vehicles, yet most annotated objects remain missed. Matched objects 3 → 54 (+51); false alarms 1 → 19; fine-tuned misses 252/306. 51 newly matched objects and 0 lost matches at this display threshold.
- **Case 07 — 0000103_01734_d_0000028.jpg:** Close-up scene: all four baseline matches are lost at this cutoff. Fine-tuning does not help this frame. Matched objects 4 → 0 (-4); false alarms 1 → 1; fine-tuned misses 4/4. 0 newly matched objects and 4 lost matches at this display threshold.
- **Case 08 — 0000023_01233_d_0000011.jpg:** Park path: fewer matched objects after fine-tuning, despite one newly matched object. Matched objects 5 → 2 (-3); false alarms 2 → 4; fine-tuned misses 12/14. 1 newly matched objects and 4 lost matches at this display threshold.
- **Case 09 — 0000024_00500_d_0000013.jpg:** Park path: fewer matches and more false alarms after fine-tuning. Matched objects 4 → 2 (-2); false alarms 1 → 2; fine-tuned misses 8/10. 0 newly matched objects and 2 lost matches at this display threshold.
- **Case 10 — 0000291_03801_d_0000887.jpg:** Busy crossing: crowded pedestrian and bicycle groups remain largely undetected. Matched objects 1 → 10 (+9); false alarms 1 → 10; fine-tuned misses 169/179. 9 newly matched objects and 0 lost matches at this display threshold.
- **Case 11 — 0000086_01954_d_0000005.jpg:** Sports courts: many small people remain missed; false alarms exceed matched detections. Matched objects 0 → 17 (+17); false alarms 0 → 30; fine-tuned misses 156/173. 17 newly matched objects and 0 lost matches at this display threshold.
- **Case 12 — 0000001_08414_d_0000013.jpg:** Plaza: more vehicle detections, but dense groups of small objects remain difficult. Matched objects 1 → 16 (+15); false alarms 0 → 10; fine-tuned misses 138/154. 15 newly matched objects and 0 lost matches at this display threshold.

## Plots

- `training-curves.png`: box and classification training loss for all 50 epochs; loss is not detection accuracy.
- `confusion-baseline.png` and `confusion-fine_tuned.png`: raw counts, with log-scaled colors. These diagnostic matrices use localization-first COCO matching after collapsing classes, then compare original labels. Crowd regions are deduplicated. Matching differs from the category-aware precision/recall above; do not sum the diagonal to recover those totals. Background column = missed objects; background row = unmatched detections. CSV versions preserve exact counts.

## Limitations and next experiments

Small, crowded objects remain a major weakness at 320px input resolution. The close-up and park scenes demonstrate regressions; improvement is not universal. One training seed, a taxonomy merge, approximate crowd-based ignore handling, and imperfect annotation coverage limit the conclusions. Overlapping VisDrone sequences also limit claims about generalization beyond this dataset.

1. Compare higher resolution and tiled inference on validation, recording latency, memory, and any changed detection limits. Rerun both development baselines under the new settings.
2. Calibrate an operating threshold on validation for an explicit precision/recall target, rather than assuming the same confidence is equally calibrated.
3. Repeat training with multiple seeds to measure variability, then investigate class imbalance for bicycles and pedestrians.
4. Evaluate inference speed separately before making real-time or deployment claims.

M5 test-dev is already consumed as the final benchmark. Do not use it to choose improvements; any new confirmatory performance claim needs a fresh holdout or a clearly disclosed follow-up protocol.

## Reproduce

Run `.venv/bin/python scripts/m6_gallery.py`, then `.venv/bin/python scripts/publish_m6_assets.py`. Requires the original local validation data, saved M3 predictions, and exported M4 predictions under `reports/m6/input`. No network or model inference is performed. Provenance and settings: `analysis.json`. All 25 tests pass, including duplicate detections, ignore regions, empty predictions, threshold filtering, and wrong-class confusion behavior.
