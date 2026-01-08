# ProbeDesign10X

Custom probe design utilities for 10x Genomics Visium and Chromium Flex (v1 + GEM‑X Flex v2), with optional NCBI BLAST off‑target screening and helpers for visualization and ordering.

## Features
- Design split 50‑nt probes (25/25) for Visium and Flex.
- Flex v2 support (GEM‑X) with correct RHS constants.
- Optional NCBI remote BLAST screening (no local BLAST dependency).
- Multi‑FASTA support with automatic concatenation.
- Matplotlib layout plotter.
- Export to IDT oPools order format.

## Requirements
- Python 3.10+
- `pandas`
- Optional: `matplotlib` (for plotting)

## Quick Start (Python)
```python
from ProbeDesign10X import design_custom_probes

seq = "ACTG..."  # 5'->3' mRNA-like sequence
df = design_custom_probes(
    seq,
    assay="visium",
    version="hd",
    max_probes=12,
)
```

### Flex v2 example
```python
df = design_custom_probes(
    seq,
    assay="flex",
    flex_version="v2",
    flex_mode="singleplex",
    flex_v2_rhs="pconst",
)
```

### Multi‑FASTA
```python
from ProbeDesign10X import design_custom_probes_from_fasta

df = design_custom_probes_from_fasta(
    "targets.fa",
    assay="visium",
    version="v2",
)
```

### Off‑target BLAST screening
```python
df = design_custom_probes(
    seq,
    assay="visium",
    version="hd",
    blast_for_offtarget=True,
    blast_organism="human",
    blast_max_hits=0,
    blast_cache_path="blast_cache.json",
)
```

### Plotting
```python
from ProbeDesign10X import plot_probe_hybridization

ax = plot_probe_hybridization(df, seq_len=len(seq), max_probes_to_plot=50)
```

### IDT oPools export
```python
from ProbeDesign10X import to_idt_opools

opools_df = to_idt_opools(df, pool_name="poolOne")
```

## CLI Usage
```bash
python ProbeDesign10X.py my_targets.fa --assay visium --version hd --out probes.csv
```

Common flags:
- `--assay visium|flex`
- `--version v1|v2|hd` (Visium)
- `--flex-version v1|v2`
- `--flex-mode singleplex|multiplex`
- `--blast` to enable NCBI BLAST screening
- `--blast-organism human|mouse|<entrez query>`
- `--plot output.png` (single‑record FASTA only)

## Notes
- The ligation constraint (A at position 25) is enforced by default; disable with `ligation_requires_a=False` or `--no-ligation-constraint`.
- BLAST screening uses NCBI’s remote API; rate limits apply.
- Flex v2 RHS constant can be set to `pconst` or `pcs1` for the Human/Mouse 4 Samples Kit.
