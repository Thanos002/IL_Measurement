# A Measurement Model for Impermanent Loss in Uniswap v3

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Thanos002/IL_Measurement/blob/main/notebooks/IL_Data_Extraction.ipynb)

Code and data for the thesis *A Measurement Model for Impermanent Loss in
Uniswap v3* by Thanos Drossos (KIT).

The project measures the realised performance of individual Uniswap v3
liquidity positions: it reconstructs each position from on-chain snapshots,
values it at mint and at burn, and decomposes the outcome into impermanent
loss (IL) and collected fees. The result is a dataset of **30,058 closed
position observations across 9 pools** between 1 May 2022 and 1 April 2024,
plus the figures and tables used in the thesis.

## Repository layout

```
.
├── notebooks/
│   └── IL_Data_Extraction.ipynb   the complete pipeline and all figures
├── data/
│   ├── pool_id_database.{pkl,csv} position IDs per pool, from The Graph
│   ├── pool_data_TVL.pkl          daily pool-level TVL, fees and volume
│   ├── full/                      per-pool position results (full window)
│   └── sample/                    same, 100 positions per pool, for quick runs
├── archive/
│   └── legacy-intermediate-data/  superseded intermediate exports, kept for provenance
├── requirements.txt
└── LICENSE
```

See [data/README.md](data/README.md) for the column-by-column description of
the datasets, and [archive/legacy-intermediate-data/README.md](archive/legacy-intermediate-data/README.md)
for what the archive contains and why it is not used.

## Getting started

```bash
pip install -r requirements.txt
jupyter lab notebooks/IL_Data_Extraction.ipynb
```

The notebook resolves its data paths relative to the repository root, so it
runs unchanged from `notebooks/` or from the repository root. Because the
committed datasets are already present, **running the notebook top to bottom
reproduces every figure without touching the network** — the fetching cells
detect the cached `.pkl` files and skip straight to loading them.

To re-run the pipeline against the small sample instead of the full dataset,
set `POOL_DATA_DIR = SAMPLE_DATA_DIR` in the *Setup: repository paths* cell.

## Re-fetching data from The Graph

The extraction code was written against The Graph's **hosted service**, which
has since been shut down. Those endpoints now return `410 Gone`, so the fetch
path in Part 1 cannot be re-run as committed — this is why the datasets are
committed rather than regenerated.

To fetch fresh data you need a Graph Network API key (free up to 100,000
queries) and the gateway URLs given in the notebook's second markdown cell:

| Subgraph | Purpose |
| --- | --- |
| `5zvR82QoaXYFyDEKLZ9t6v9adgnptxYpKpSbxtgVENFV` | Uniswap v3 (pool day data, token prices) |
| `GqzP4Xaehti8KSfQmv3ZctFSjnSUYZ4En5NRsiTbvZpz` | revert-finance Uniswap v3 mainnet (position snapshots) |

Substitute your key into the gateway URL and point `url_uniswap` and the
`Extract_data` URL at it.

## Pipeline

The notebook runs in four stages, each its own section:

| Section | What it does |
| --- | --- |
| Setup | Resolves repository and data paths |
| Part 1.1 | Fetches position snapshots and pool day data from the subgraphs |
| Part 1.2 | Normalises the nested GraphQL responses, attaches hourly USD prices, and computes IL, fees and return per position |
| Part 1.3 | Runs the pipeline over individual positions as a worked example |
| Part 2 | Produces the figures and tables used in the thesis |

The closing *Appendix* section holds an earlier class-based implementation that
computed fees and IL directly from Uniswap v3 tick math. It is superseded by the
pandas pipeline in Part 1, is not executed anywhere, and does not run as-is.

## Pools studied

Nine mainnet pools, chosen to span three volatility regimes and several fee
tiers. Observations are burn events, so a position that was closed in several
steps contributes more than one row.

| Pool | Fee tier | Category | Address | Observations |
| --- | --- | --- | --- | --- |
| DAI/USDC | 0.01% | stable/stable | `0x5777d92f208679db4b9778590fa3cab3ac9e2168` | 816 |
| DAI/USDC | 0.05% | stable/stable | `0x6c6bc977e13df9b0de53b251522280bb72383700` | 414 |
| USDC/WETH | 0.01% | stable/risky | `0xe0554a476a092703abdb3ef35c80e0d76d32939f` | 473 |
| USDC/WETH | 0.05% | stable/risky | `0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640` | 7,017 |
| USDC/WETH | 0.3% | stable/risky | `0x8ad599c3a0ff1de082011efddc58f1908eb6e6d8` | 6,791 |
| USDC/WETH | 1% | stable/risky | `0x7bea39867e4169dbe237d55c8242a8f2fcdcc387` | 690 |
| WBTC/WETH | 0.05% | risky/risky | `0x4585fe77225b41b697c938b018e2ac67ac5a20c0` | 6,727 |
| WBTC/WETH | 0.3% | risky/risky | `0xcbcdf9626bc03e24f779434178a73a0b4bad62ed` | 7,043 |
| MKR/WETH | 1% | risky/risky | `0x3afdc5e6dfc0b0a507a8e023c9dce2cafc310316` | 87 |

Observation window: blocks 14,691,320 to 19,560,244 (1 May 2022 to 1 April 2024).

## License

[MIT](LICENSE) © 2024 Thanos Drossos
