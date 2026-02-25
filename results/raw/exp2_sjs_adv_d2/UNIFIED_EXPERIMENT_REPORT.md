# 合成数据集性能测试统一结果文档（exp2_sjs_adv_d2）

- 生成时间：2026-02-25T08:44:01
- 数据来源目录：`/home/dhy/PhD/Comp-26.2.23/Join-Sampling-2D/results/raw/exp2_sjs_adv_d2/manifest/merged`
- 汇总表目录：`/home/dhy/PhD/Comp-26.2.23/Join-Sampling-2D/results/raw/exp2_sjs_adv_d2/summary_tables`
- 点位中位数明细：`point_model_medians.csv`
- 胜率汇总：`sweep_winrates.csv`
- E 预算诊断明细：`E_budget_detail.csv`

## 1. 统计口径与数据完整性

- 端到端耗时：`wall_ms`（单次 repeat）。
- 查询/采样阶段耗时：`run_sample_ms`（来自 `phases_json`，用于近似“查询时间”口径）。
- 本文默认按参数点 + 模型取 `median`（重复次数通常为 3），并用 `speedup = baseline_ms / ours_ms`（>1 表示 ours 更快）。

| 文件 | 总行数 | OK行数 | OK率 |
|---|---:|---:|---:|
| `A_alpha_F0_merged.csv` | 189 | 189 | 100.0% |
| `A_alpha_F1_merged.csv` | 189 | 189 | 100.0% |
| `B_N_F0_merged.csv` | 126 | 126 | 100.0% |
| `C_t_F0_merged.csv` | 147 | 147 | 100.0% |
| `D_family_F0_merged.csv` | 21 | 21 | 100.0% |
| `D_family_F1_merged.csv` | 21 | 21 | 100.0% |
| `E_budget_F0_merged.csv` | 294 | 294 | 100.0% |
| `F_high_alpha_F0_merged.csv` | 45 | 45 | 100.0% |

## 2. 全局结论（先看结论）

### 2.1 ours 对 kd_tree（胜率）

| Sweep | 点位数 | ours(3变体) wall | ours(3变体) query | ours(sampling+adaptive) wall | ours(sampling+adaptive) query | median speedup(query, s+a vs kd) |
|---|---:|---:|---:|---:|---:|---:|
| A_alpha_F0 | 9 | 1/9 | 9/9 | 1/9 | 8/9 | x1.47 |
| A_alpha_F1 | 9 | 4/9 | 9/9 | 3/9 | 9/9 | x2.25 |
| B_N_F0 | 6 | 2/6 | 6/6 | 2/6 | 6/6 | x1.42 |
| C_t_F0 | 7 | 3/7 | 7/7 | 3/7 | 6/7 | x1.33 |
| D_family_F0 | 1 | 0/1 | 1/1 | 0/1 | 1/1 | x1.31 |
| D_family_F1 | 1 | 1/1 | 1/1 | 1/1 | 1/1 | x3.52 |
| E_budget_F0 | 24 | 0/2 | 0/2 | 0/2 | 0/2 | x0.30 |
| F_high_alpha_F0 | 3 | 3/3 | 3/3 | 3/3 | 3/3 | x74.57 |

### 2.2 ours 对全部 baseline（kd + range_tree）

| Sweep | ours(3变体) wall | ours(3变体) query | ours(sampling+adaptive) wall | ours(sampling+adaptive) query |
|---|---:|---:|---:|---:|
| A_alpha_F0 | 1/9 | 9/9 | 1/9 | 2/9 |
| A_alpha_F1 | 4/9 | 6/9 | 3/9 | 4/9 |
| B_N_F0 | 2/6 | 4/6 | 2/6 | 6/6 |
| C_t_F0 | 3/7 | 5/7 | 3/7 | 6/7 |
| D_family_F0 | 0/1 | 1/1 | 0/1 | 1/1 |
| D_family_F1 | 1/1 | 1/1 | 1/1 | 1/1 |
| E_budget_F0 | 22/24 | 22/24 | 22/24 | 22/24 |
| F_high_alpha_F0 | 3/3 | 0/3 | 3/3 | 0/3 |

- 观察：你们方法在 `query(run_sample_ms)` 口径明显更强；`wall_ms` 的优势主要集中在高密度/大 t 区间。

## 3. 分组结果

### 3.1 A*：α_out 变化（F0/F1）

#### A_alpha_F0

| α_out | 最优wall(全模型) | 最优query(全模型) | ours(s+a) wall | kd wall | speedup wall(ours/kd) | ours(s+a) query | kd query | speedup query(ours/kd) |
|---:|---|---|---:|---:|---:|---:|---:|---:|
| 0.1 | kd_tree/sampling / 2050.6 ms | ours/enum_sampling / 33.7 ms | 7652.1 | 2050.6 | x0.27 | 1221.1 | 1324.6 | x1.08 |
| 0.3 | kd_tree/sampling / 2389.7 ms | ours/enum_sampling / 36.0 ms | 7598.1 | 2389.7 | x0.31 | 1261.5 | 1622.2 | x1.29 |
| 1 | kd_tree/sampling / 2992.8 ms | ours/enum_sampling / 49.2 ms | 8091.5 | 2992.8 | x0.37 | 1345.5 | 2154.2 | x1.60 |
| 3 | kd_tree/sampling / 3433.5 ms | ours/enum_sampling / 126.4 ms | 8457.5 | 3433.5 | x0.41 | 1329.4 | 2489.1 | x1.87 |
| 10 | kd_tree/sampling / 4134.5 ms | ours/enum_sampling / 145.6 ms | 9000.7 | 4134.5 | x0.46 | 1323.3 | 2956.4 | x2.23 |
| 30 | kd_tree/sampling / 5466.5 ms | ours/enum_sampling / 149.3 ms | 12574.4 | 5466.5 | x0.43 | 4697.5 | 3845.3 | x0.82 |
| 100 | kd_tree/sampling / 8599.4 ms | ours/enum_sampling / 154.3 ms | 13153.4 | 8599.4 | x0.65 | 5505.1 | 6021.9 | x1.09 |
| 300 | kd_tree/sampling / 12258.1 ms | ours/enum_sampling / 167.1 ms | 13461.3 | 12258.1 | x0.91 | 5700.9 | 8357.7 | x1.47 |
| 1000 | ours/sampling / 13906.4 ms | ours/enum_sampling / 232.6 ms | 13906.4 | 20699.4 | x1.49 | 1196.2 | 13680.1 | x11.44 |

#### A_alpha_F1

| α_out | 最优wall(全模型) | 最优query(全模型) | ours(s+a) wall | kd wall | speedup wall(ours/kd) | ours(s+a) query | kd query | speedup query(ours/kd) |
|---:|---|---|---:|---:|---:|---:|---:|---:|
| 0.1 | kd_tree/sampling / 2081.8 ms | range_tree/enum_sampling / 37.6 ms | 7306.2 | 2081.8 | x0.28 | 1237.9 | 1327.7 | x1.07 |
| 0.3 | kd_tree/sampling / 2480.7 ms | ours/enum_sampling / 35.6 ms | 7620.8 | 2480.7 | x0.33 | 1257.2 | 1652.9 | x1.31 |
| 1 | kd_tree/sampling / 3277.0 ms | ours/enum_sampling / 53.2 ms | 8070.0 | 3277.0 | x0.41 | 1308.4 | 2294.9 | x1.75 |
| 3 | kd_tree/sampling / 4266.1 ms | ours/enum_sampling / 123.6 ms | 8476.0 | 4266.1 | x0.50 | 1326.2 | 2987.4 | x2.25 |
| 10 | kd_tree/sampling / 6323.3 ms | range_tree/enum_sampling / 145.1 ms | 8902.5 | 6323.3 | x0.71 | 1318.0 | 4281.4 | x3.25 |
| 30 | ours/enum_sampling / 8850.2 ms | ours/enum_sampling / 149.1 ms | 12346.9 | 10449.4 | x0.85 | 4557.9 | 6837.2 | x1.50 |
| 100 | ours/enum_sampling / 10923.3 ms | range_tree/enum_sampling / 159.4 ms | 13845.2 | 20163.3 | x1.46 | 5504.7 | 13065.3 | x2.37 |
| 300 | ours/sampling / 14480.0 ms | ours/enum_sampling / 168.3 ms | 14480.0 | 39615.5 | x2.74 | 5729.2 | 25463.5 | x4.44 |
| 1000 | ours/sampling / 14049.1 ms | ours/enum_sampling / 229.0 ms | 14049.1 | 85969.7 | x6.12 | 5988.4 | 56012.7 | x9.35 |

### 3.2 B*：N 变化（F0）

| N | 最优wall(全模型) | ours(s+a) wall | kd wall | speedup wall(ours/kd) | ours(s+a) query | kd query | speedup query(ours/kd) |
|---:|---|---:|---:|---:|---:|---:|---:|
| 100000 | ours/enum_sampling / 1054.1 ms | 1301.2 | 1823.6 | x1.40 | 842.7 | 1594.9 | x1.89 |
| 200000 | ours/enum_sampling / 2242.1 ms | 2430.4 | 2529.4 | x1.04 | 1283.1 | 2064.1 | x1.61 |
| 500000 | kd_tree/sampling / 4803.0 ms | 6433.8 | 4803.0 | x0.75 | 2906.6 | 3471.3 | x1.19 |
| 1000000 | kd_tree/sampling / 10396.7 ms | 13397.6 | 10396.7 | x0.78 | 5584.5 | 7118.3 | x1.27 |
| 2000000 | kd_tree/sampling / 23077.6 ms | 27080.7 | 23077.6 | x0.85 | 10481.4 | 14835.6 | x1.42 |
| 5000000 | kd_tree/sampling / 64063.5 ms | 74175.8 | 64063.5 | x0.86 | 27055.8 | 38789.3 | x1.43 |

### 3.3 C*：t 变化（F0）

| t | ours/sampling count(ms) | kd count(ms) | ours/sampling query(ms) | kd query(ms) | speedup query(ours/kd) | ours query吞吐(samples/s) | kd query吞吐(samples/s) |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 1000000 | 4861.8 | 3098.2 | 3774.6 | 3756.4 | x1.00 | 264927 | 266211 |
| 3000000 | 4834.5 | 3072.3 | 4335.5 | 5828.1 | x1.34 | 691968 | 514746 |
| 10000000 | 4833.7 | 3066.1 | 5513.9 | 7097.8 | x1.29 | 1813593 | 1408893 |
| 30000000 | 4813.2 | 3047.7 | 7795.8 | 10089.5 | x1.29 | 3848202 | 2973394 |
| 100000000 | 4806.6 | 3216.0 | 15468.2 | 20507.4 | x1.33 | 6464874 | 4876284 |
| 300000000 | 4841.9 | 3056.7 | 37444.2 | 49632.5 | x1.33 | 8011931 | 6044427 |
| 1000000000 | 4797.2 | 3232.2 | 114903.4 | 153553.3 | x1.34 | 8702962 | 6512396 |

- 观察：`run_count_ms` 上 kd 更低，但 `run_sample_ms` 随 t 增大后，ours/sampling 稳定优于 kd，最终带来高 t 的 wall 反超。

### 3.4 D*：数据族敏感性（F0 vs F1）

| 数据族 | 最优wall(全模型) | ours(s+a) wall | kd wall | speedup wall(ours/kd) | 最优query(全模型) | ours(s+a) query | kd query | speedup query(ours/kd) |
|---|---|---:|---:|---:|---|---:|---:|---:|
| F0 | kd_tree/sampling / 10544.6 ms | 13167.3 | 10544.6 | x0.80 | ours/enum_sampling / 161.8 ms | 5510.8 | 7219.7 | x1.31 |
| F1 | ours/sampling / 13374.6 ms | 13374.6 | 30342.3 | x2.27 | ours/enum_sampling / 161.2 ms | 5589.6 | 19664.3 | x3.52 |

### 3.5 E：预算诊断（t ∈ {1e5,3e5}，B×w_small）

| t | B | w_small | ours最佳wall模型 | ours最佳wall(ms) | kd wall(ms) | speedup wall | ours最佳query模型 | ours最佳query(ms) | kd query(ms) | speedup query | ours/adaptive phase3_residual(ms) | phase3占run_sample比 |
|---:|---:|---:|---|---:|---:|---:|---|---:|---:|---:|---:|---:|
| 100000 | 10000000 | 256 | ours/sampling | 11345.3 | - | - | ours/sampling | 3453.4 | - | - | 3505.5 | 97.9% |
| 100000 | 10000000 | 512 | ours/sampling | 11157.8 | - | - | ours/sampling | 3451.7 | - | - | 3469.0 | 97.6% |
| 100000 | 10000000 | 1024 | ours/sampling | 11276.0 | - | - | ours/sampling | 3479.3 | - | - | 3501.5 | 97.5% |
| 100000 | 20000000 | 256 | ours/sampling | 11261.1 | - | - | ours/sampling | 3473.3 | - | - | 3487.3 | 97.6% |
| 100000 | 20000000 | 512 | ours/sampling | 11182.1 | - | - | ours/sampling | 3458.2 | - | - | 3459.8 | 97.6% |
| 100000 | 20000000 | 1024 | ours/sampling | 11186.2 | - | - | ours/sampling | 3476.3 | - | - | 3511.1 | 97.6% |
| 100000 | 50000000 | 256 | ours/sampling | 11181.7 | - | - | ours/sampling | 3449.7 | - | - | 3537.8 | 97.3% |
| 100000 | 50000000 | 512 | ours/sampling | 11220.1 | 3845.5 | x0.34 | ours/sampling | 3461.6 | 561.5 | x0.16 | 3501.2 | 97.4% |
| 100000 | 50000000 | 1024 | ours/sampling | 11237.9 | - | - | ours/sampling | 3462.1 | - | - | 3469.1 | 97.6% |
| 100000 | 100000000 | 256 | ours/sampling | 11130.0 | - | - | ours/sampling | 3439.3 | - | - | 3468.2 | 97.5% |
| 100000 | 100000000 | 512 | ours/sampling | 11238.7 | - | - | ours/sampling | 3462.7 | - | - | 3473.4 | 97.5% |
| 100000 | 100000000 | 1024 | ours/sampling | 11129.0 | - | - | ours/sampling | 3442.0 | - | - | 3442.7 | 97.6% |
| 300000 | 10000000 | 256 | ours/sampling | 11220.0 | - | - | ours/sampling | 3517.6 | - | - | 3569.1 | 96.9% |
| 300000 | 10000000 | 512 | ours/adaptive | 11499.9 | - | - | ours/sampling | 3557.0 | - | - | 3551.8 | 96.4% |
| 300000 | 10000000 | 1024 | ours/sampling | 11252.3 | - | - | ours/sampling | 3516.9 | - | - | 3575.8 | 96.9% |
| 300000 | 20000000 | 256 | ours/sampling | 11252.6 | - | - | ours/sampling | 3532.8 | - | - | 3537.5 | 96.8% |
| 300000 | 20000000 | 512 | ours/sampling | 11315.1 | - | - | ours/sampling | 3558.0 | - | - | 3555.8 | 96.9% |
| 300000 | 20000000 | 1024 | ours/sampling | 11274.8 | - | - | ours/sampling | 3543.8 | - | - | 3547.8 | 96.7% |
| 300000 | 50000000 | 256 | ours/sampling | 11382.6 | - | - | ours/sampling | 3558.7 | - | - | 3531.1 | 96.7% |
| 300000 | 50000000 | 512 | ours/sampling | 11296.9 | 4820.9 | x0.43 | ours/sampling | 3545.9 | 1532.8 | x0.43 | 3542.5 | 96.8% |
| 300000 | 50000000 | 1024 | ours/sampling | 11198.3 | - | - | ours/sampling | 3499.9 | - | - | 3747.6 | 96.3% |
| 300000 | 100000000 | 256 | ours/sampling | 11314.9 | - | - | ours/sampling | 3531.1 | - | - | 3499.1 | 96.4% |
| 300000 | 100000000 | 512 | ours/sampling | 11410.7 | - | - | ours/sampling | 3544.2 | - | - | 3518.6 | 96.7% |
| 300000 | 100000000 | 1024 | ours/sampling | 11356.0 | - | - | ours/sampling | 3550.3 | - | - | 3494.3 | 96.7% |

- 观察：在这批结果中，`B` 与 `w_small` 变化对小 t 点位的 wall/query 改善不明显；`phase3_fill_residual` 占比仍较高，和你的诊断假设一致。

### 3.6 F：高密度补充（α_out = 1000/3000/10000）

| α_out | wall赢家 | sample赢家 | ours/sampling wall(ms) | kd wall(ms) | speedup wall(ours/kd) | ours/adaptive sample(ms) | range_tree/adaptive sample(ms) | kd sample(ms) | speedup sample(ours_adaptive/kd) |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1000 | ours/sampling / 12254.7 ms | range_tree/adaptive / 167.8 ms | 12254.7 | 14686.3 | x1.20 | 169.4 | 167.8 | 7663.6 | x45.24 |
| 3000 | ours/sampling / 12410.7 ms | range_tree/adaptive / 163.8 ms | 12410.7 | 23746.3 | x1.91 | 170.9 | 163.8 | 12740.0 | x74.57 |
| 10000 | ours/sampling / 12837.5 ms | range_tree/adaptive / 165.9 ms | 12837.5 | 40747.8 | x3.17 | 167.9 | 165.9 | 22137.7 | x131.87 |

- 观察：高密度区 `wall_ms` 明显由 ours/sampling 占优；`sample_ms` 上 range_tree/adaptive 略优于 ours/adaptive，但两者都远快于 kd。

## 4. 可直接写进正文的结论

1. “查询阶段”上，ours 相比 kd 在绝大多数点位更快，优势在高密度/大 t 区间最显著。
2. “端到端”上，ours 的优势不是全域的：低密度或小 t 时 kd 仍有常数项优势。
3. 与 range_tree 比较，ours 在 wall 上整体更稳，但 query 最快点位在少数设置会被 range_tree/adaptive 以小幅度领先。
4. E 预算诊断显示当前小 t 仍受 residual 阶段影响，后续可继续围绕 residual 指标做实现级优化。

## 5. 限制与说明

- `run_sample_ms` 来自 phase 计时，本文将其作为“查询/采样阶段时间”的近似指标。
- E 组所需的 `residual_cnt/residual_ratio/pass2_used` 尚未在 CSV 中显式列出；本报告用 `phase3_fill_residual_ms` 作为替代信号。
- `enum_sampling` 在部分点位会显著降低 query 时间；若需公平对比“纯在线采样”，请优先看 `sampling+adaptive` 口径。