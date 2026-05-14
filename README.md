# FlexENN: A Graph Neural Network for Binding Energy Prediction of Globular and Intrinsically Disordered Proteins

A graph neural network that predicts the experimental
binding free energy ΔG of protein–protein complexes from their PDB structures.

Each complex is converted into a residue-level interface graph: nodes are
Cα atoms of residues at the binding interface, edges connect spatially
nearby residues within and across the two partner chains, and node features
encode amino-acid identity, hydrophobicity, charge, backbone dihedrals,
B-factor, distance to the partner chain, and a partner-chain flag. The
network passes messages along these edges using relative 3D positions, mean-
pools the resulting node embeddings, and predicts a single scalar ΔG.

---

[![Workflow](assets/FlexENN_architecture.png)](assets/FlexENN_architecture.png)

## Project structure

```
.
├── src/                        
│   └── gnn_pyg.ipynb
├── data/                      
│   └── IDP_complexes.csv
│   └── IDP-dataset-example
│   └── Folded_complexes.csv
│   └── Folded-dataset-example
│   └── benchmark-set-example                  
├── README.md
├── environment.yml            # conda environment
└── .gitignore
```

---

## Installation

### Option A — conda (recommended)

```bash
conda env create -f environment.yml
conda activate idp-gnn
```


> **CPU-only PyTorch.** The pinned dependencies install the CPU build of
> PyTorch. For a CUDA build, follow the official selector at
> <https://pytorch.org/get-started/locally/> and the matching PyG wheel
> instructions at <https://pytorch-geometric.readthedocs.io/en/latest/install/installation.html>.

---

## Data format
Download the relevant files from PDBbind+ (https://www.pdbbind-plus.org.cn/)

The notebook expects a CSV with at least these columns:

| column                | description                                           |
|-----------------------|-------------------------------------------------------|
| `pdb_id`              | four-letter PDB ID, used to locate `<pdb_id>.pdb`     |
| `interacting_chains`  | partner chains in `AB_CD` form (left of `_` is partner 1, right is partner 2) |
| `dG_exp`              | experimental binding free energy in kcal/mol          |

The PDB files themselves live in a folder pointed to by `pdb_folder` in
the notebook. Update both paths near the top of the notebook:

```python
csv_path = "../data/IDP_complexes.csv"
pdb_folder = "../data/IDP-dataset-example/"
```
The folder right now consists of a few examples of the dataset pdbs used to train the model.
---

## Usage

After downloading the dataset and making the relevant files forIDPs and Folded complexes, open the notebook and run cells top-to-bottom:

```bash
jupyter notebook gnn_pyg.ipynb
```

The notebook is organised in numbered sections:

1. **Imports & environment check** — verifies versions.
2. **Reproducibility** — seeds all RNGs.
3. **Dataset configuration** — CSV / PDB folder paths.
4. **Metadata loading** — reads the CSV and resolves PDB paths.
5. **Residue extraction** — parses PDBs.
6. **Interface detection** — 8 Å Cα–Cα cutoff.
7. **Node features** — 29-dim per-residue vectors.
8. **Edges** — intra-chain (≤ 8 Å) and inter-chain (≤ 4.5 Å).
9. **Graph assembly** — packs features, edges, coordinates, and ΔG label.
10. **Build dataset** — runs the pipeline over every complex (slow cell).
11. **Sanity checks** — node / edge counts, ΔG label spot-check.
12. **EGNN model** — message-passing layer + readout head.
13. **DataLoader & training loop**.
14. **Train**.
15. **Evaluation** — MSE, MAE, Pearson r
16. **Inference** — predict ΔG on benchmark set.

---

## Citation

If you use this code in academic work, please cite the original publication
(...).

---

