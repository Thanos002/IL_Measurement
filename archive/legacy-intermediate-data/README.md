# Legacy intermediate data

**Nothing here is used by the notebook or the thesis.** These are 45 superseded
intermediate exports from the development of the extraction pipeline, kept only
as a record of how the dataset took shape. The results the thesis reports live
in [`../../data/`](../../data/).

This folder was called `Backup/` at the repository root before the January 2025
cleanup.

## What is in here

Files are dated 19 April to 30 May 2024. The duplicate-numbered names are
browser download suffixes — each `(n)` is a later re-export of the same pool,
not a different pool.

| Group | Dates | What changed |
| --- | --- | --- |
| `*_normalized_data.csv` (9, unsuffixed) | 19–20 Apr | Earliest exports. Full snapshot schema — every subgraph column and every event row, before `shorten_df` reduced the output to burn events and 50 columns. Comma-separated. |
| `*_normalized_data (2).csv` (9) | 2 May | First exports in the current 50-column schema, comma-separated. Only a handful of rows each — the pipeline was still being developed. |
| `*_normalized_data (3).csv` (9) | 11–13 May | Fee attribution and liquidity matching reworked. |
| `*_normalized_data (4).csv` (8) | 13–14 May | Semicolon separator and comma decimal mark adopted, matching the final format. |
| `*_normalized_data (5).csv` (3) | 14 May | Last partial re-export before the final run. |
| `DAI_USDC_500_normalized_data.xlsx` and `(version 1)` | 19–20 Apr | Excel exports of the early schema. |
| `pool_id_database.csv`, `pool_id_database (2).csv` | 25 Apr, 2 May | Earlier position-ID databases, superseded by `../../data/pool_id_database.csv`. |
| `pool_data_TVL.pkl` | 30 May | Earlier, shorter TVL cache, superseded by `../../data/pool_data_TVL.pkl`. |
| `normalized_df.csv` | 25 Apr | Two-row debug dump from the single-position exploratory cell in Part 1.2. |
| `WBTC_WETH_3000.csv` | 13 May | One-off export that never picked up the `_normalized_data` suffix. |

Because the schema, the separator and the row filtering all changed over this
period, these files are **not comparable to each other or to the final
dataset**. Read `../../data/README.md` for the schema that is actually
documented and used.
