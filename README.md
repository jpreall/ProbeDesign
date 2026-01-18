# ProbeDesign10X

<p><strong><a href="https://jpreall.github.io/ProbeMaker/" style="font-size: 1.4em;">Try the browser version</a></strong></p>

Probe design utilities for 10x Genomics Visium and Chromium Flex (v1 and GEM-X Flex v2). It designs split 50-nt probes, can optionally screen for off-targets using NCBI BLAST, and provides helpers for plotting and IDT oPools ordering.

## Input options

You can provide sequence input in two ways:

1) A single sequence string (5'->3' mRNA-like) using `design_custom_probes(...)`
2) A FASTA path (single or multi-record) using `design_custom_probes_from_fasta(...)`

Multi-FASTA behavior:
- Each record is designed independently.
- Results are concatenated into one DataFrame.
- `sequence_id` is added when more than one record is present.
- Probe IDs are prefixed by the FASTA header unless `probe_name_prefix` is supplied.

Key assay settings:
- `assay`: `visium` or `flex`
- Visium: `version` = `v1`, `v2`, or `hd`
- Flex: `flex_version` = `v1`, `v2`, or `v2_4plex`
  - v1: `flex_mode` = `singleplex` or `multiplex`
  - v1 multiplex only: `flex_barcode` = `BC001`..`BC016`
  - v2: pConst RHS (standard singleplex/multiplex)
  - v2_4plex: pCS1 RHS (4-sample kit)

Design knobs you might care about:
- `max_probes`, `min_spacing`
- `gc_min`, `gc_max`
- `ligation_requires_a` (TN ligation constraint; default True)
- `auto_pick` (force 3 well-spaced probes, emphasize GC near 50%)

Off-target screening (optional):
- `blast_for_offtarget=True` to enable NCBI remote BLAST
- `blast_organism` can be `human`, `mouse`, or any Entrez query string
- `blast_max_hits` controls filtering
- `blast_cache_path` caches results to avoid re-querying

## Examples

### 1) Basic Visium design (Python)
```python
from ProbeDesign10X import design_custom_probes

seq = "ACTG..."  # 5'->3' mRNA-like sequence
df = design_custom_probes(seq, assay="visium", version="hd", max_probes=12)
```

### 2) Flex v2 design (Python)
```python
df = design_custom_probes(
    seq,
    assay="flex",
    flex_version="v2",
    flex_mode="singleplex",
)
```

### 3) Multi-FASTA (Python)
```python
from ProbeDesign10X import design_custom_probes_from_fasta

df = design_custom_probes_from_fasta(
    "targets.fa",
    assay="visium",
    version="v2",
)
```

### 4) Off-target BLAST screening (Python)
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

### 5) Command line (FASTA input)
```bash
python ProbeDesign10X.py targets.fa --assay visium --version hd --out probes.csv
```

### 6) Plotting and IDT oPools export
```python
from ProbeDesign10X import plot_probe_hybridization, to_idt_opools

plot_probe_hybridization(df, seq_len=len(seq), max_probes_to_plot=50)
opools_df = to_idt_opools(df, pool_name="poolOne")
```

License: MIT