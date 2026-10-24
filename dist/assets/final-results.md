# M5 — final test-dev results

Both models were evaluated once on the same 1,610 images using the frozen settings. These are project COCO-style scores, not official VisDrone leaderboard scores.

| Shared-class metric | COCO baseline | Fine-tuned | Change (percentage points) |
|---|---:|---:|---:|
| AP50 | 2.21% | 15.09% | +12.88 |
| AP | 1.29% | 7.57% | +6.28 |
| AP_S | 0.21% | 1.76% | +1.54 |
| AP_M | 2.06% | 11.50% | +9.43 |
| AP_L | 10.54% | 24.56% | +14.02 |

## Shared categories

| Category | Baseline AP | Fine-tuned AP | Change (pp) |
|---|---:|---:|---:|
| person | 1.35% | 2.22% | +0.87 |
| bicycle | 0.00% | 0.05% | +0.05 |
| car | 3.75% | 21.47% | +17.72 |
| truck | 1.34% | 5.27% | +3.92 |
| bus | 1.02% | 14.66% | +13.64 |
| motorcycle | 0.29% | 1.78% | +1.49 |

## Fine-tuned: all ten VisDrone categories

| Category | AP@0.5:0.95 | AP@0.5 |
|---|---:|---:|
| pedestrian | 2.22% | 6.25% |
| people | 0.55% | 1.61% |
| bicycle | 0.05% | 0.26% |
| car | 20.11% | 39.66% |
| van | 3.06% | 4.63% |
| truck | 5.12% | 9.15% |
| tricycle | 1.41% | 3.22% |
| awning-tricycle | 0.87% | 1.46% |
| bus | 14.45% | 25.54% |
| motor | 1.72% | 5.24% |

| Full ten-class metric | Score |
|---|---:|
| AP | 4.96% |
| AP50 | 9.70% |
| AP_S | 1.09% |
| AP_M | 7.54% |
| AP_L | 18.12% |

## Checks and limitations

- Detector-cap hits: baseline 0, fine-tuned 0.
- Scorer-cap truncations: baseline 0, fine-tuned 0.
- Shared-category AP regressions: none observed.
- One seed and one final split; no statistical-significance claim.
- Both final models ran on the same Colab CPU runtime. Model weights, settings, ground-truth hashes, and software versions are recorded.
- Crowd-based ignore handling approximates VisDrone ignore rules. Merged pedestrian/people predictions retain native class-aware NMS behavior.
- Final-test results were not used for tuning. Validation and test-dev metrics describe different image sets and must not be conflated.
