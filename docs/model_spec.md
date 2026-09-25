# LUSP-YOLO Model Specification

## 1. Compression path

```text
YOLO11s
  → LUSP-YOLO-Base
  → Shared-5 structured pruning
  → post-pruning recovery
```

## 2. LUSP-YOLO-Base

The base variant changes only two selected high-level C3k2 blocks:

- `model.8`: `C3k2` → `C3k2PConv`
- `model.22`: `C3k2` → `C3k2PConv`

The PConv specification uses partial ratio `rho = 0.25`, expansion `alpha = 1.0`, a 3×3 partial branch, and a residual shortcut. The high-resolution pathways and the three-scale detection interface remain intact.

## 3. PConv block

`C3k2PConv` combines a partial-convolution branch with an identity branch, applies pointwise channel mixing after the partial operation, and retains the block-level residual/shortcut behavior. The public configuration specifies the architectural contract; the implementation is not included in this preview.

## 4. Shared-5 roots

The five direct pruning roots are:

```text
model.7
model.8.cv2
model.10.cv2
model.20
model.22.cv2
```

The local ratio is `r = 0.22`. Channels are ranked with the absolute BatchNorm scale (`|gamma|`), dependency propagation is enabled, and retained widths are aligned to multiples of eight.

## 5. Protected components

The Detect head, prediction widths, DFL interface, and PConv channel split are protected. This keeps the output contract valid and prevents an invalid partial/identity branch partition.

## 6. Recovery and training scope

Formal post-pruning recovery uses 50 epochs of AdamW with initial learning rate `7.14e-5`, `patience = 0`, and no distillation during recovery. Knowledge distillation is a training condition for the BDD100K Base parent, not a structural module and not part of the post-pruning recovery recipe.

## 7. Benchmark-specific parent training

The structural policy is shared, while parent training is benchmark-specific:

- KITTI: the S5-noKD parent is used.
- BDD100K: the reported Base parent is the KD-trained S5 Base; post-pruning recovery itself does not use KD.

The two benchmarks instantiate prediction heads for eight and ten classes respectively. Thus the backbone/neck design and pruning policy are shared, but the final prediction-head tensors are benchmark-specific.

## 8. Cross-benchmark setup

The same Base replacement and Shared-5 target manifest are applied independently to KITTI and BDD100K. This tests policy-level transfer across different class taxonomies and traffic distributions without claiming that the resulting checkpoints are bitwise identical.
