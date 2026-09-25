# Preview Consistency Check

The following cross-file checks were completed:

- README Base replacements match `configs/lusp_yolo_base.yaml`: `model.8` and `model.22`.
- README Shared-5 roots match `configs/shared5_pruning.yaml`: five roots with `r = 0.22`.
- `docs/model_spec.md` repeats the same Base, protected components, recovery schedule, and benchmark-specific parent-training caveat.
- Main-result rows in README and `results/benchmark_results.md` use the same rounded values.
- GFLOPs are labelled as fused-model measurements; latency is labelled as FP16 batch-one forward time.
- KITTI and BDD100K are described as sharing the policy while using independent weights and class-specific prediction heads.
- The BDD final standalone-validation discrepancy is explicitly recorded in both the results note and the audit.

No automatic git add, commit, push, repository synchronization, checkpoint upload, or dataset copy was performed.
