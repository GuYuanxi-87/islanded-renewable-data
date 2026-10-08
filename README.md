# islanded-renewable-data

Supporting data for **Tiered carbon pricing and weather sensitivity in planning islanded renewable power with hybrid storage**.

## Current deposit status

The three CSV files currently in this repository contain the numerical tables reported in the manuscript, transcribed at their displayed precision. They are not a replacement for the complete hourly data.

Raw meteorology, unrounded results, all 31 successful solutions, full hourly dispatch, verification reports, and calculation scripts remain in the previously prepared archive. Do not describe this repository as a complete deposit until that archive is present and checked.

Updated 8 October 2026.

## 中文说明

本仓库用于保存论文结论的支持资料。它对应重新建立的公开数据算例，不是对丢失的原始仿真文件的恢复。

当前已经存放论文中的五场景成本与排放表、容量表，以及另一气象年份的供电检验表。数值保留论文展示精度。完整逐时数据与计算程序仍待上传，当前不能将仓库写成已经完整公开全部研究数据。

气象资料来自 NASA POWER。负荷由明确规则构造。设备费用、排放系数、抽蓄水头和容量边界含有明确假设。排放结果是筛选用的温室气体排放代理量，不是当地实测碳足迹或正式生命周期清单。

## Study and data boundary

- Weather location: 40.9 degrees N, 94.7 degrees E, near Dunhuang, China.
- Source time standard: UTC. Constructed demand uses UTC+8 calendar fields.
- Weather sequences: all 8760 hours of 2023 and all 8784 hours of 2024.
- Public meteorological fields: WS50M, T2M, and ALLSKY_SFC_SW_DWN.
- Constructed demand: annual mean 20 MW, explicitly defined daily and seasonal cosine patterns.
- Equipment: wind, horizontal-array PV proxy, reversible pumped hydro, battery, electrolyzer, hydrogen containment, and fuel cell.
- No external electricity or fuel imports.
- Continuous capacity and hourly dispatch optimization using SciPy/HiGHS.
- Study horizon: 20 years. Assumed real discount rate: 5%.
- Resource NPC includes capital, discounted O&M and replacements. It excludes the hypothetical carbon payment.
- GHG proxy includes assumed equipment and replacement factors, without discounting.
- Carbon pricing is a hypothetical one-time internal planning charge. It is not a statutory annual emissions-trading settlement.
- Weather tests retain perfect foresight and optimized periodic initial inventories. They do not certify long-term reliability.

## Currently available tables

| File | Manuscript content | Precision and scope |
| --- | --- | --- |
| [scenario_results_reported.csv](tables/scenario_results_reported.csv) | Main Table III | Five 2023 planning scenarios, displayed precision |
| [capacities_reported.csv](tables/capacities_reported.csv) | Main Table II | All nine capacities of those five scenarios, displayed precision |
| [weather_adequacy_reported.csv](tables/weather_adequacy_reported.csv) | Main Table IV | Fixed-capacity 2024 tests and joint two-year adequacy |

These tables were transcribed from the finalized manuscript prepared on 7 October 2026. In the complete computational archive, the original JSON summaries retain unrounded numerical results.

## Findings supported by the reported tables

Under the stated assumptions:
- S2 and S3 have the same selected capacities and reported cost/GHG results. The higher S3 price tiers are inactive at the flat-price minimizer.
- Moving from S2 to S4 reduces the GHG proxy by 12.54% and raises resource NPC by 3.82%, calculated from the unrounded results.
- S4 and S5 have unchanged capacities and resource/GHG results, although their hypothetical carbon payments differ.
- Fixed 2023 S1 and S4 capacities leave 2.073% and 3.452% of 2024 demand unserved.
- The joint design meets both modeled weather years, at 13.49% higher resource NPC than the single-year S4 design.

Rounded tables can produce slightly different recomputed percentages. Use the unrounded archived JSON summaries for arithmetic reconciliation.

## Complete archive contents awaiting deposit

The prepared local archive is named `JRSE计算与投稿资料.zip`. The deposit scope is its `reproducible_case/` directory and the root `SHA256SUMS.txt` manifest, packaged as `islanded-renewable-data.zip`. Within `reproducible_case/` are:
- exact raw NASA POWER responses for both years and source URLs/download date;
- processed hourly inputs with units and the constructed load;
- base assumptions and component cost/GHG coefficients;
- all 31 successful scenario, sensitivity, removal, sampled-price, and weather-test summaries;
- all corresponding complete `*_dispatch.npz` arrays;
- readable hourly CSV exports for the main and weather cases;
- independent numerical verification and the separate physical-control record;
- `solve_case.py`, `verify_case.py`, `make_figures.py`;
- exact software versions, installation requirements, figure files, and reference metadata.

The original archive also contains manuscript copies in a separate `manuscripts/` directory. That directory is outside this data deposit scope. The retained original SHA-256 manifest lists both data and manuscript files; checks of this deposit should select entries beginning with `reproducible_case/`.

## Computational environment and verification

The prepared archive records Python 3.12.14, NumPy 2.3.5, SciPy 1.16.3 and Matplotlib 3.10.7.

Its 31 successful solutions passed independent checks of hourly power balance, periodic storage updates, component costs, assumed GHG totals, piecewise carbon charges, simultaneous operating modes, and curtailment. Those records validate numerical consistency, not physical site feasibility or the empirical validity of assumed carbon factors.

A separate 24-hour periodic test with no renewable generation and no imports was correctly infeasible. An earlier full-year control test reached its time limit and was inconclusive; it is not counted as a passed test.

After the complete archive is deposited, extract it, work inside `reproducible_case`, install `requirements.txt` in Python 3.12, and run:

```text
python solve_case.py --phase base
python solve_case.py --phase scenarios
python solve_case.py --phase weighted
python solve_case.py --phase sensitivity
python solve_case.py --phase validate
python solve_case.py --phase ablation
python solve_case.py --phase extended
python verify_case.py
python make_figures.py
```

The default behavior solves the cases. The optional `--reuse` argument instead loads existing successful saved outputs. The unused `pareto` phase is not part of the article.

Use the named native-unit arrays in each NPZ, not its internal diagnostic `x` vector. Flow arrays are MW, pumped-storage state is MWh of gravitational potential, battery state is MWh, and hydrogen state is kg. Each state is at the start of an hourly interval. Each weather year has a separate periodic state cycle.

## Sources

- [NASA POWER hourly API documentation](https://power.larc.nasa.gov/docs/services/api/temporal/hourly/)
- [Exact 2023 API query](https://power.larc.nasa.gov/api/temporal/hourly/point?parameters=ALLSKY_SFC_SW_DWN,WS50M,T2M&community=RE&longitude=94.7&latitude=40.9&start=20230101&end=20231231&format=JSON&time-standard=UTC)
- [Exact 2024 API query](https://power.larc.nasa.gov/api/temporal/hourly/point?parameters=ALLSKY_SFC_SW_DWN,WS50M,T2M&community=RE&longitude=94.7&latitude=40.9&start=20240101&end=20241231&format=JSON&time-standard=UTC)

Raw responses in the prepared archive were downloaded on 7 October 2026. Parameter provenance and limitations are described in the manuscript and supplementary material. Public reference frameworks do not turn the assumed equipment factors into measured local inventories.
