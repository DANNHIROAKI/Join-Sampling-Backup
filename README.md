# Join Sampling 2D Version

## Implemented methods (Dim = 2)

### Our method (Chapter 2 primitives)
* `ours/enum_sampling` — Framework I (enumerate full join, then uniform index sampling)
* `ours/sampling` — Framework II (two-pass, exact i.i.d. sampling with replacement)
* `ours/adaptive` — Framework III (budgeted full-cache + prefetch sample-cache; optional 2nd pass for residual)

### Comparison method 1: orthogonal range tree (Chapter 4 primitives)
* `range_tree/enum_sampling` — Framework I
* `range_tree/sampling` — Framework II
* `range_tree/adaptive` — Framework III (budgeted full-cache + prefetch sample-cache)

### Comparison method 2: kd-tree on the 2d-dimensional embedding (Chapter 5)
* `kd_tree/sampling` — sampling-only baseline (exact range COUNT + range SAMPLE join)

## Key knobs (Framework III)

* `--j_star=<u64>` (or `extra["budget"]`): cache budget **B** (max stored partner records)
* `extra["w_small"]=<u64>`: full-cache threshold (cache blocks with `w_e <= w_small`)

If `w_small=0`, adaptive behaves like Framework II (no caching).

## Build

```bash
mkdir -p build
cd build
cmake ..
cmake --build . -j
```

## Run

The `run/` folder contains experiment scripts:

```bash
bash run/run_exp0.sh
bash run/run_exp1.sh
bash run/run_exp2.sh
bash run/run_exp3.sh
bash run/run_exp4.sh
bash run/run_exp5.sh
bash run/run_exp6.sh
bash run/run_exp7.sh
```

You can also run the CLI tools (built from `apps/`):
* `sjs_run` — run one method/variant on a dataset and write results
* `sjs_verify` — small-scale correctness + sampling-quality checks
