# CLAUDE.md

Guidance for Claude Code working in this repository.

## What this is

A measurement model for impermanent loss (IL) in Uniswap v3, backing the SAC '25
paper *Automated Market Makers: Toward More Profitable Liquidity Provisioning
Strategies* and the underlying thesis. It reconstructs individual liquidity
positions from on-chain snapshots, values them at mint and burn, and decomposes
the outcome into IL and collected fees.

There is **no application and no test suite** — the deliverable is one notebook
plus the datasets it produced. Treat both as published research artefacts.

- [README.md](README.md) — layout, how to run it, the nine pools, the paper
- [data/README.md](data/README.md) — all 50 columns defined, file by file

Read those before changing anything; they are the reference and this file does
not repeat them.

## Layout

```
notebooks/IL_Data_Extraction.ipynb   the entire pipeline and all figures
data/            pool_id_database, pool_data_TVL, full/ (30,058 rows), sample/ (1,121)
archive/         superseded intermediate exports — inert, never read as data
```

## The notebook

It is 8.3 MB, of which 8.0 MB is **59 embedded PNG figures — the published
results**. Never strip outputs, never "clean" the notebook by re-running it, and
never clear execution counts. The size is justified.

### Editing it

Edit the JSON programmatically rather than by hand, and serialize exactly this
way — it round-trips the file byte-identically, so a one-line change produces a
one-line diff instead of an 8 MB one:

```python
json.dumps(nb, ensure_ascii=False, indent=2, sort_keys=True) + "\n"
```

Assert every replacement (`assert src.count(old) == 1`) so a missed match fails
loudly instead of silently producing a broken notebook.

### Rules that matter

- **Data paths.** Access files only through `DATA_DIR`, `FULL_DATA_DIR` and
  `SAMPLE_DATA_DIR`, defined in the *Setup: repository paths* cell. They resolve
  against the repository root, found by walking up from the working directory,
  so the notebook runs from `notebooks/` or from the repository root. Never
  reintroduce bare filenames — that silently breaks one of the two entry points.
  `POOL_DATA_DIR` switches the pipeline between `full/` and `sample/`.
- **The network is gone.** The extraction code targets The Graph's hosted
  service, which now returns `410 Gone`. The datasets are committed precisely so
  the notebook reproduces every figure offline. Do not try to re-run the fetch
  path or "repair" it; fresh data needs a Graph Network API key and the gateway
  subgraph IDs listed in the README.
- **The Appendix cell is deliberately dead.** The last cell is a superseded
  class-based implementation. It is executed by nothing and does not parse (a
  stray `:` on a `super().__init__(...)` call). It is kept as a record — do not
  run it, fix it, or delete it.
- **Dead code lives inside `"""…"""` blocks** in the normalization cells, e.g. a
  `to_csv('normalized_df.csv')` that is inside a string, not live. Check whether
  a line is actually executed before acting on it.

## Reading the data

- Prefer the `.pkl` files; they keep dtypes and are what the notebook loads.
- The CSVs are written for a German Excel locale:
  `pd.read_csv(path, sep=";", decimal=",")`.
- **One row is one burn event, not one position.** A position closed in several
  steps contributes several rows.
- `IL_total_abs`, `IL_total_rel`, `IL_annualized_total` and `IL_formula_total`
  are position-level aggregates broadcast onto every row of that position —
  **never sum them across rows.**
- `archive/legacy-intermediate-data/` differs in schema, separator and row
  filtering across its own files. It is provenance only, never input.

## Verifying a notebook change

```bash
python -c "import nbformat; nb=nbformat.read('notebooks/IL_Data_Extraction.ipynb',as_version=4); nbformat.validate(nb); print(len(nb.cells),'cells,',sum(1 for c in nb.cells for o in c.get('outputs',[]) if 'image/png' in (o.get('data') or {})),'figures')"
```

Expect **137 cells and 59 figures**. If the figure count drops, outputs were
lost — do not commit. Then confirm the data still loads from both entry points
by running the *Setup: repository paths* logic from `notebooks/` and from the
repository root.

## Conventions

- **Do not add a Claude co-author trailer to commits.** The user asked for this
  explicitly.
- Python 3.11; dependencies in `requirements.txt`. There is no lockfile and no
  CI.
- Windows host. Git prints `LF will be replaced by CRLF` warnings on these text
  files; that is expected and not an error.
- The canonical remote is `github.com/ThanosDrossos/IL_Measurement`. The former
  `Thanos002` account still redirects, but new links should use the current one.
