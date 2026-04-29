# TabPFN for Power Grid

This repository explores [TabPFN](https://github.com/PriorLabs/TabPFN) — a foundation model for tabular data — applied to power grid problems.



## Set up an env 

```bash
uv init --name tabpfn-grid
```

Import existing dependencies and create UV lock file

```bash
uv add -r requirements.txt
uv lock
```

```bash
uv sync --frozen
```


## Structure

### ACPF/ — AC Power Flow Prediction

Notebooks predicting AC power flow solutions across different grid topologies using TabPFN.

| Notebook | Description |
|----------|-------------|
| `tabpfn_ieee.ipynb` | TabPFN on IEEE test cases |
| `tabpfn_ieee_multiload.ipynb` | Multi-load scenario predictions on IEEE grids |
| `tabpfn_cross_grid.ipynb` | Cross-grid generalization experiments |
| `tabpfn_cross_grid_300.ipynb` | Cross-grid with 300-bus systems |
| `tabpfn_cross_grid_clean.ipynb` | Cleaned cross-grid analysis |

### NTO/ — Network Topology Optimization

Notebooks using TabPFN to classify grid topology actions in a reinforcement learning setting (Grid2Op).

| Notebook | Description |
|----------|-------------|
| `tabpfn_topology_grid2op.ipynb` | TabPFN classifier for topology actions with ensemble strategy (n_estimators=8, subsampling) |
| `tabpfn_nto_investigation.ipynb` | Feature importance ablation, multi-label classification, threshold tuning, and per-substation analysis |
