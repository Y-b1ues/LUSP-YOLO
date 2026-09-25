# Preview Audit

## Scope

This audit records which facts were checked before creating the public preview. The preview does not copy private implementation, checkpoints, datasets, logs, or server paths.

## Source cross-checks

- **Model YAML:** the formal YOLO11s configuration places `C3k2PConv` at `model.8` and `model.22` with partial ratio 0.25 and expansion 1.0.
- **Custom module source:** the formal PConv module defines the partial-convolution block and its channel-split contract.
- **Formal validation:** standalone validation JSON files were checked for the KITTI S0, KITTI Core+M7-R22, BDD S0, BDD Base, and BDD Core+M7-R22 checkpoints.
- **Inference profiler:** formal FP16 fused batch-one JSON files were checked for parameters, latency, and protocol fields.
- **GFLOPs audit:** the existing unified profiler audit establishes the fused Base values of 20.432230 GFLOPs (KITTI) and 20.436531 GFLOPs (BDD100K). The compact public tables round both Base values to 20.4 and use the paper's rounded 19.9 value for the final Core+M7-R22 endpoint.

## Verified structural facts

- Base model: YOLO11s.
- Base replacements: `model.8` and `model.22`, `C3k2` → `C3k2PConv`.
- PConv partial ratio: 0.25; expansion: 1.0.
- Shared-5 direct roots: `model.7`, `model.8.cv2`, `model.10.cv2`, `model.20`, `model.22.cv2`.
- Local pruning ratio: 0.22; importance: `|gamma|` from BatchNorm; width alignment: 8; dependency propagation: enabled.
- Protected: Detect head, prediction widths, DFL interface, and PConv channel split.
- Recovery: 50 epochs, AdamW, initial learning rate 7.14e-5, patience 0, no KD during recovery.

## Result-source notes

The compact main table follows the latest formal validation/profiler and manuscript values. The BDD final Core+M7-R22 row is rounded from the standalone validation result 0.5246138 mAP50 and 0.2927871 mAP50-95; an earlier draft contained 0.5291/0.2959, so the latest standalone result is used and the discrepancy is explicitly recorded.

The BDD Base parent is KD-trained, whereas the KITTI Base parent is noKD. This is a benchmark-specific parent-training condition; the structure and post-pruning recovery specification remain the same. KD is not used during formal recovery.

## Figures

No approved local source for Fig. 2 or Fig. 3 was found. The preview therefore contains an asset note instead of fabricated or unrelated images.

## Deliberately excluded

Full code, pruning scripts, recovery scripts, KD implementation, datasets, checkpoints, raw logs, credentials, and private absolute paths are not included.
