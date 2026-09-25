# LUSP-YOLO

**Lightweight Unified Structure-aware Pipeline for Efficient Traffic Object Detection**

This repository currently provides a preliminary public release of LUSP-YOLO, including the model specification, Shared-5 structured-pruning policy, evaluation protocol, and reported benchmark results. The complete source code, training and pruning scripts, evaluation tools, and pretrained checkpoints will be released after the review process.

## Overview

LUSP-YOLO starts from YOLO11s, replaces two selected high-level C3k2 blocks with low-expansion partial-convolution blocks, applies dependency-aware Shared-5 channel pruning, and performs low-learning-rate post-pruning recovery. The design coordinates selective P5 redesign with topology-preserving structured compression for traffic detection.

```text
YOLO11s → LUSP-YOLO-Base → Shared-5 structured pruning → 50-epoch recovery
```

## Method at a glance

### LUSP-YOLO-Base

- `model.8`: `C3k2` → `C3k2PConv`
- `model.22`: `C3k2` → `C3k2PConv`
- partial ratio `rho = 0.25`
- expansion `alpha = 1.0`
- three-scale prediction interface is retained

### Shared-5

The public pruning specification contains five direct roots:

```text
model.7, model.8.cv2, model.10.cv2, model.20, model.22.cv2
```

The local channel ratio is `r = 0.22`; importance is based on the absolute BatchNorm scale (`|gamma|`), selected widths are aligned to multiples of eight, and dependency propagation is enabled. The Detect head, DFL interface, prediction widths, and PConv channel split are protected.

## Main results

| Dataset | Model | Params (M) | GFLOPs | mAP50 | mAP50-95 | Latency (ms) |
|---|---|---:|---:|---:|---:|---:|
| KITTI | YOLO11s | 9.416 | 21.3 | 0.9188 | 0.7287 | 4.949 |
| KITTI | LUSP-YOLO-Base | 8.309 | 20.4 | 0.9282 | 0.7368 | 4.343 |
| KITTI | LUSP-YOLO | 7.411 | 19.9 | 0.9276 | 0.7352 | 4.707 |
| BDD100K | YOLO11s | 9.417 | 21.3 | 0.5328 | 0.2987 | 5.090 |
| BDD100K | LUSP-YOLO-Base (KD parent) | 8.310 | 20.4 | 0.5341 | 0.3002 | 4.830 |
| BDD100K | LUSP-YOLO | 7.412 | 19.9 | 0.5246 | 0.2928 | 4.828 |

The full table, recovery controls, and metric definitions are in [results/benchmark_results.md](results/benchmark_results.md). The BDD final row uses the latest standalone validation result, as detailed in `PREVIEW_AUDIT.md`.

## Evaluation protocol

- input size: `640 × 640`
- post-pruning recovery: 50 epochs, AdamW, initial learning rate `7.14e-5`, no early stopping
- reported latency: PyTorch FP16, fused model, batch one, model-forward only
- reported GFLOPs: fused-model profile at `640 × 640`
- the selected best checkpoint is independently re-evaluated before reporting

## Framework figure

The source audit did not find approved local exports for the manuscript framework and Pareto figures. They are therefore not fabricated or replaced with unrelated images; see `assets/README.md` and `RELEASE_STATUS.md`.

## Repository status

See [RELEASE_STATUS.md](RELEASE_STATUS.md). This is a specification-and-results preview, not a complete reproduction package.

## Citation

Citation information will be updated after the review/publication process.

## License

The final license will be added together with the complete implementation release.
