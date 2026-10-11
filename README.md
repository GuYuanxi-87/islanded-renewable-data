# Islanded renewable power planning datasets

This repository provides the numerical datasets supporting **Tiered carbon pricing and weather sensitivity in planning islanded renewable power with hybrid storage**.

The study uses wind and photovoltaic generation, pumped hydro, batteries and hydrogen storage. It examines carbon-price thresholds, capacity decisions and supply adequacy across two weather years. The numerical case was calculated on 7 October 2026, and the complete dataset was deposited on 11 October 2026.

## Download and data version

[Download the complete dataset](https://raw.githubusercontent.com/GuYuanxi-87/islanded-renewable-data/5f9b9d40c0e11ad91d35954efa89c03122e4cda5/islanded-renewable-datasets.zip), then extract the ZIP archive.

- Dataset file `islanded-renewable-datasets.zip`
- Size 14,042,164 bytes
- Fixed dataset commit `5f9b9d40c0e11ad91d35954efa89c03122e4cda5`
- SHA-256 `f7214471bc342f86c088d686183e9acd68dce1c4f7f985cb26729f558acc9ccd`

The archive contains **83 numerical dataset files and two metadata files**, giving 85 entries. It includes summaries and complete hourly arrays for **31 successful cases**.

The [fixed dataset version](https://github.com/GuYuanxi-87/islanded-renewable-data/tree/5f9b9d40c0e11ad91d35954efa89c03122e4cda5) preserves the numerical data used in the manuscript. Subsequent documentation updates can be viewed on the main branch.

## Data sources and assumptions

Weather data are the original hourly JSON responses downloaded from [NASA POWER](https://power.larc.nasa.gov/) on 7 October 2026. The requested point is 40.9° N, 94.7° E, near Dunhuang, China.

| Item | Description |
| --- | --- |
| Weather periods | Full calendar years 2023 and 2024 |
| Number of hourly records | 8,760 in 2023 and 8,784 in 2024 |
| Source time standard | UTC |
| Weather variables | `WS50M`, wind speed at 50 m; `T2M`, air temperature at 2 m; `ALLSKY_SFC_SW_DWN`, surface solar irradiation |
| Processed inputs | Estimated wind speed at 100 m, wind and PV availability, and hourly electricity demand |
| Demand | Constructed profile, normalized to a mean of 20 MW separately for each year; daily variation uses UTC+8 |
| Study period | 20 years, with a 5% discount rate |

Exact download URLs and the retrieval date are recorded in `data_sources.json`. The weather records are gridded data. Demand, equipment costs, component emission factors, storage conditions and capacity limits are disclosed assumptions. These files document a newly calculated illustrative case. The earlier lost simulation files have not been recovered.

## File guide

Paths in the following table are relative to the extracted dataset folder. `YEAR` and `CASE` indicate the corresponding year and case name.

| File or pattern | Content |
| --- | --- |
| `nasa_power_YEAR.json` | Two original NASA POWER responses |
| `inputs_YEAR.csv` | Two processed hourly input tables, with units in the column names |
| `assumptions.json` | Engineering, demand, economic and GHG assumptions |
| `model_coefficients.json` | Discounted cost and equipment/replacement GHG coefficients |
| `data_sources.json` | Weather coordinates, time standard, download date and exact source URLs |
| `data_quality.json` | Input coverage and quality information |
| `case_results.csv` | Comparison of all 31 successful cases |
| `CASE_summary.json` | 31 summaries containing capacities, scenario parameters, costs, GHG totals and annual energy results at saved numerical precision |
| `CASE_dispatch.npz` | 31 compressed NumPy files with complete hourly power flows and storage states |
| `readable_dispatch/` | Nine hourly CSV exports for the five main cases, two fixed-capacity weather tests and both years of the joint plan |
| `independent_verification.json` | Independent numerical checks for all 31 successful solutions |
| `physical_control_check.json` | Record of the separate no-generation, no-import control |
| `environment_versions.json` | Numerical software and solver versions |
| `dataset_description.json` | Data provenance, units, conventions and interpretation |
| `dataset_inventory.csv` | Sizes and SHA-256 checksums for the 83 numerical dataset files |

The repository also provides three rounded manuscript tables in [`tables/`](tables/). Use the JSON summaries and full dataset archive when saved numerical precision or hourly records are needed.

| Repository table | Content |
| --- | --- |
| [`capacities_reported.csv`](tables/capacities_reported.csv) | Reported capacity decisions |
| [`scenario_results_reported.csv`](tables/scenario_results_reported.csv) | Reported main-scenario cost and GHG results |
| [`weather_adequacy_reported.csv`](tables/weather_adequacy_reported.csv) | Reported weather-year adequacy results |

## Case names

| Case or prefix | Meaning |
| --- | --- |
| `S1_unpriced` | Base plan without a carbon price |
| `S2_flat` | Flat carbon pricing |
| `S3_inactive_tiers` | Tiered pricing with inactive higher-price bands |
| `S4_active_tiers` | Tiered pricing with active higher-price bands |
| `S5_tighter_budget` | Tiered pricing with a tighter carbon budget |
| `S1_unpriced_2024_validation`, `S4_active_tiers_2024_validation` | Fixed 2023 capacities evaluated with 2024 weather |
| `two_year_active` | Shared capacities planned for both weather years |
| `frontier_price_*` | Additional sampled flat-price cases |
| `sens_*` | Carbon-price, engineering and demand sensitivity cases |
| `ablation_*` | Cases excluding a storage technology |
| `extended_*` | Additional storage-capacity and component-GHG sensitivity cases |

## Units and array conventions

All intervals have a duration of one hour. Hourly flow arrays use nonnegative magnitudes, with charging and discharging stored separately.

| NPZ array | Unit |
| --- | --- |
| `pump`, `turbine` | MW |
| `battery_charge`, `battery_discharge` | MW |
| `electrolyzer`, `fuelcell` | MW |
| `curtail`, `unserved` | MW |
| `phs_state` | MWh of gravitational potential energy |
| `battery_state` | MWh |
| `hydrogen_state` | kg of usable hydrogen |

Storage states refer to the **start of each hourly interval**. Each weather year has a periodic inventory cycle. The joint plan uses shared capacities and separate annual inventory cycles. Its hourly arrays place 2023 before 2024, giving 17,544 intervals in total.

The nine entries in the NPZ `capacities` array follow this order.

1. Wind power, MW
2. PV power, MW DC
3. Pumped-hydro power, MW
4. Pumped-hydro gravitational potential storage, MWh
5. Battery power, MW
6. Battery nameplate energy, MWh
7. Electrolyzer power, MW
8. Usable hydrogen storage, kg
9. Fuel-cell power, MW

JSON capacity keys and CSV column names also identify these units. `GHG_proxy_tCO2e` uses tonnes of CO2 equivalent. Cost fields ending in `_USD` use illustrative constant US dollars. The carbon charge is a hypothetical one-time charge on the assumed 20-year equipment and replacement inventory. Resource net present cost excludes that charge.

Use the named physical arrays for analysis. The NPZ `x` vector and some early diagnostic residuals use internal solver scaling. Consistent checks in physical units are provided in `independent_verification.json`.

## Read the data

CSV and JSON files can be read with common spreadsheet or text tools. NPZ files can be read with Python and NumPy. After extracting the archive to a folder named `data`, the following example reads the S4 results.

```python
from pathlib import Path
import json
import numpy as np

folder = Path("data")
summary = json.loads(
    (folder / "S4_active_tiers_summary.json").read_text(encoding="utf-8")
)
print(summary["capacities"])
print(summary["NPC_USD"])
print(summary["GHG_proxy_tCO2e"])

with np.load(folder / "S4_active_tiers_dispatch.npz", allow_pickle=False) as hourly:
    print(hourly.files)
    print(hourly["hydrogen_state"][:24])
    # Each MW value covers one hour, so its sum is energy in MWh.
    print(float(hourly["unserved"].sum()))
```

The calculation environment used Python 3.12.14, NumPy 2.3.5 and SciPy 1.16.3 with bundled HiGHS. `environment_versions.json` records the full environment. This repository provides the numerical data and documentation. The optimization implementation is supplied separately with the manuscript's computational materials.

## Verification and interpretation

All 31 saved successful solutions passed independent checks of hourly power balance, periodic storage updates, cost and GHG accounting, carbon charges, simultaneous operating modes and curtailment limits. The recorded tolerances and residuals can be inspected in `independent_verification.json`.

The separate 24-hour periodic control with no generation and no imports was infeasible. An earlier full-year control reached its time limit and was inconclusive.

The following conditions apply when interpreting the results.

- Demand is constructed, and the equipment GHG factors are screening assumptions. The GHG indicator describes the model's equipment and replacement inventory. It does not establish a site-specific life cycle assessment or measured local carbon footprint.
- Fixed-capacity 2024 tests minimize unserved energy with perfect foresight and optimized cyclic starting inventories. Their financial-intensity and carbon-payment fields are left blank in `case_results.csv`.
- Annual minimum unserved energy is the weather-adequacy indicator used in the study. Maximum hourly shortage and shortage duration can depend on the chosen optimal dispatch.
- Two weather years support the reported feasibility comparisons. They do not establish statistical reliability or engineering deployment readiness.

## Citation and data availability

Suggested dataset reference

> GuYuanxi-87 (2026). Datasets for islanded renewable power planning with tiered carbon pricing. GitHub, dataset version `5f9b9d4`. https://github.com/GuYuanxi-87/islanded-renewable-data/tree/5f9b9d40c0e11ad91d35954efa89c03122e4cda5

Suggested data availability statement

> The numerical datasets supporting the findings of this study are openly available on GitHub in the islanded-renewable-data repository, dataset version 5f9b9d4, at https://github.com/GuYuanxi-87/islanded-renewable-data/tree/5f9b9d40c0e11ad91d35954efa89c03122e4cda5.

Please cite NASA POWER as the source of the meteorological inputs, using the download details in `data_sources.json`. Questions about the dataset can be submitted through the repository's [Issues page](https://github.com/GuYuanxi-87/islanded-renewable-data/issues).

## 中文说明

本仓库保存论文结论所使用的数值数据。完整压缩包包括83份数值数据和2份说明及校验文件，共85个条目。31个成功方案都有结果汇总和完整逐时数组。仓库中的 `tables/` 是经过舍入的论文表格，需要完整精度时请使用压缩包中的数据。

气象资料来自 NASA POWER，研究点位于敦煌附近。2023年和2024年分别有8760小时和8784小时的数据。负荷按明确规则构造，两个年份的平均功率均为20 MW。工程成本和排放系数采用已披露的算例假设。现有文件记录的是重新建立并计算的公开数据算例，原先丢失的仿真文件没有恢复。

下载压缩包后，先阅读 `dataset_description.json` 和 `assumptions.json`。CSV可直接查看，NPZ可用上面的Python示例读取。储能状态对应每小时开始时刻。两个天气年份分别满足年度周期条件，跨年份不转移储能库存。排放指标用于筛选分析，两个天气年份的比较也不能作为长期供电可靠性的认证。

固定数据版本为 `5f9b9d4`。主分支可以继续更新说明，论文引用的数据版本保持不变。
