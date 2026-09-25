# Benchmark Results

## 1. Main results

All rows use the fused-model convention for parameters and GFLOPs. GFLOPs are rounded to one decimal place in the compact table; the exact Base audit is 20.432230 GFLOPs for KITTI and 20.436531 GFLOPs for BDD100K. Latency is PyTorch FP16 batch-one model-forward time on the same RTX 4090 protocol.

| Dataset | Model | Params (M) | GFLOPs | mAP50 | mAP50-95 | Latency (ms) |
|---|---|---:|---:|---:|---:|---:|
| KITTI | YOLO11s | 9.416 | 21.3 | 0.9188 | 0.7287 | 4.949 |
| KITTI | LUSP-YOLO-Base (S5-noKD) | 8.309 | 20.4 | 0.9282 | 0.7368 | 4.343 |
| KITTI | LUSP-YOLO (Core+M7-R22 transfer) | 7.411 | 19.9 | 0.9276 | 0.7352 | 4.707 |
| BDD100K | YOLO11s | 9.417 | 21.3 | 0.5328 | 0.2987 | 5.090 |
| BDD100K | LUSP-YOLO-Base (KD parent) | 8.310 | 20.4 | 0.5341 | 0.3002 | 4.830 |
| BDD100K | LUSP-YOLO (Core+M7-R22) | 7.412 | 19.9 | 0.5246 | 0.2928 | 4.828 |

The BDD100K LUSP-YOLO row uses the latest independently re-evaluated formal checkpoint (`mAP50 = 0.5246`, `mAP50-95 = 0.2928`); this supersedes an earlier rounded draft value of 0.5291/0.2959. The change is documented in `PREVIEW_AUDIT.md` rather than silently mixing sources.

## 2. KITTI Shared-5 recovery controls

| Method | r | Params (M) | AP / mAP50-95 | Ret.@5 | Ret.@10 | E98 | E99 |
|---|---:|---:|---:|---:|---:|---:|---:|
| YOLO11s direct | 0.22 | 8.52 | 0.7216 | 97.93% | 98.17% | 8 | 30 |
| YOLO11s direct-PM | 0.49 | 7.44 | 0.7204 | 96.19% | 97.89% | 20 | >50 |
| LUSP-YOLO | 0.22 | 7.41 | 0.7352 | 98.15% | 98.79% | 5 | 9 |

These are recovery-control statistics derived from per-epoch validation logs; they are not substitutes for the independently re-evaluated main checkpoints.

## 3. Metric protocol

- The selected best checkpoint is re-evaluated independently before the main result is recorded.
- Latency is FP16 batch-one model-forward time; preprocessing, postprocessing, and NMS are excluded.
- GFLOPs use the fused-model profile at 640×640 with the same custom profiler and fuse state.
- KITTI and BDD100K use benchmark-specific class counts in the prediction head, so absolute metrics are not cross-dataset difficulty scores.
