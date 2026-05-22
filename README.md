# Pol II PTM Information-Capacity — Reproducibility Package

> **Fully reproducible computation of RNA Polymerase II post-translational modification information capacity, from raw database downloads to every figure, table, and numeric result in the manuscript.**

[![](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![](https://img.shields.io/badge/License-CC--BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/) [![DOI](https://zenodo.org/badge/1246792910.svg)](https://doi.org/10.5281/zenodo.20345702)

---

### Table of contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Requirements](#requirements)
- [Repository Structure](#repository-structure)
- [Notebook 1 — PTM and Protein Sequence Data Retrieval](#notebook-1--ptm-and-protein-sequence-data-retrieval)
- [Notebook 2 — Information-Theoretic Calculations](#notebook-2--information-theoretic-calculations)
- [Data Sources](#data-sources)
- [Key Results](#key-results)
- [Authors](#authors)
- [Citation](#citation)
- [License](#license)

---

## Overview

This repository contains two Jupyter notebooks: `notebook_1.ipynb` (Supplementary Data File 1) and `notebook_2.ipynb` (Supplementary Data File 2) that together reproduce the datasets used and the numeric values and tables reported in the main text and in Supplementary Information S1 and S3. `notebook_1.ipynb` can additionally generate fresh versions of the two .tsv files provided together with the Jupyter notebooks in this repository: `uniprot_polII_seqs_2026_05_22.tsv` (Supplementary Data File 3) and ``ptms_polII_`2026_05_22`.tsv`` (Supplementary Data File 4), provided the retrieved upstream (online) databases did not change. The calculation integrates six public PTM databases plus literature-mined entries to build a comprehensive atlas of human RNA Polymerase II post-translational modifications, then applies information-theoretic methods to quantify the regulatory capacity encoded by those modifications.

<table>
<thead>
<tr>
<th>Notebook</th>
<th>Modules</th>
<th>Purpose</th>
<th>Expected runtime</th>
</tr>
</thead>
<tbody><tr>
<td>notebook_1.ipynb</td>
<td>1 – 10</td>
<td>PTM atlas construction from public databases</td>
<td>~6 min</td>
</tr>
<tr>
<td>notebook_2.ipynb</td>
<td>11 – 18</td>
<td>Information-theoretic calculations and all manuscript outputs</td>
<td>~1 s</td>
</tr>
</tbody></table>

Modules are numbered continuously across both notebooks and must be run **sequentially** within each notebook. Module numbers correspond directly to the order of calculations in the manuscript and Supplementary Information S1 and S3.

---

## Quick Start

### Replicating manuscript results (offline)

Download the two supplementary .tsv data files and place them in the working directory:

<table>
<thead><tr><th>File</th><th>Description</th></tr></thead>
<tbody><tr><td>uniprot_polII_seqs_2026_05_22.tsv</td><td>Version-pinned UniProt sequences (Supplementary Data File 3)</td></tr><tr><td>ptms_polII_2026_05_22.tsv</td><td>Frozen PTM atlas (Supplementary Data File 4)</td></tr></tbody></table>

When both files are present, every network download in Notebook 1 is automatically skipped and Notebook 2 reads directly from the snapshots. All audit assertions in both Notebooks are pinned to these snapshots.

### Full pipeline replication (network required)

Simply run Notebook 1 without the cached files. Each module will query its respective upstream database, the frozen Pol II subunit protein sequences will be written to `uniprot_polII_seqs_{CURRENT DATE}.tsv` and the frozen PTM atlas will be written to `ptms_polII_{CURRENT DATE}.tsv` for future offline runs.

> NOTE: The audit assertions in Notebook 2 are pinned to the bundled snapshots and **will fail** if run against a freshly regenerated atlas (upstream databases may have been updated).

---

## Requirements

- Notebook 1: pandas, requests, beautifulsoup4, IPython
- Notebook 2: pandas, IPython

Install all dependencies:

```bash
pip install pandas requests beautifulsoup4 ipython
```

---

## Repository Structure

```
├── notebook_1.ipynb                      # Supplementary Data File 1 — Notebook 1: database downloads & atlas build
├── notebook_2.ipynb                      # Supplementary Data File 2 — Notebook 2: information-capacity calculations
├── uniprot_polII_seqs_2026_05_22.tsv     # Supplementary Data File 3 — pinned protein sequences
├── ptms_polII_2026_05_22.tsv             # Supplementary Data File 4 — frozen PTM atlas
├── README.md                             # Reproducibility instructions
```

---

## Notebook 1 — PTM and Protein Sequence Data Retrieval

### Environment check (optional)

Verifies all software dependencies and aborts with a diagnostic message if any requirement is missing or below the required version.

### Module 1 · Canonical UniProt sequence retrieval

Downloads the 12 human Pol II subunit sequences from UniProt UniSave with hard-coded version numbers, guaranteeing byte-identical output across runs. Writes `uniprot_polII_seqs_{CURRENT DATE}.tsv` if no cache is found.

**Pinned UniProt versions:**

<table>
<thead>
<tr>
<th>Accession</th>
<th>Version</th>
<th>Subunit</th>
</tr>
</thead>
<tbody><tr>
<td>P24928</td>
<td>248</td>
<td>Rpb1</td>
</tr>
<tr>
<td>P30876</td>
<td>231</td>
<td>Rpb2</td>
</tr>
<tr>
<td>P19387</td>
<td>235</td>
<td>Rpb3</td>
</tr>
<tr>
<td>O15514</td>
<td>211</td>
<td>Rpb4</td>
</tr>
<tr>
<td>P19388</td>
<td>237</td>
<td>Rpb5</td>
</tr>
<tr>
<td>P61218</td>
<td>187</td>
<td>Rpb6</td>
</tr>
<tr>
<td>P62487</td>
<td>178</td>
<td>Rpb7</td>
</tr>
<tr>
<td>P52434</td>
<td>230</td>
<td>Rpb8</td>
</tr>
<tr>
<td>P36954</td>
<td>221</td>
<td>Rpb9</td>
</tr>
<tr>
<td>P62875</td>
<td>187</td>
<td>Rpb10</td>
</tr>
<tr>
<td>P52435</td>
<td>215</td>
<td>Rpb11</td>
</tr>
<tr>
<td>P53803</td>
<td>209</td>
<td>Rpb12</td>
</tr>
</tbody></table>

### Module 2 · UniProt manually curated PTM annotations

Retrieves manually curated "Modified residue" features from UniProtKB REST for all 12 accessions and converts them to the canonical `(Substrate, Residue, PTM)` schema shared by all download modules.

### Module 3 · UniProt large-scale proteomics PTMs

Queries the EBI Proteins API for mass-spectrometry-derived PTM evidence, resolving peptide-relative positions to global protein positions.

### Module 4 · dbPTM experimental PTMs

Parses all `.gz` archives from the dbPTM experiment directory, extracting modified residue identities.

### Module 5 · iPTMnet integrated PTMs

Loads the iPTMnet integrated score release and filters to the 12 Pol II accessions, including the `P24928-1` alias used internally by iPTMnet.

### Module 6 · PhosphoSitePlus PTMs

Harvests PTM evidence via HTML scraping, restricting to human, non-isoform sites. Uses a hard-coded "accession → internal PhosphoSitePlus ID" mapping.

### Module 7 · GlyGen glycosylation and phosphorylation annotations

Queries the GlyGen protein-detail endpoint using isoform-1 identifiers, collecting both glycosylation sites with a defined subtype (such as O-GlcNAcylation) and phosphorylation sites.

### Module 8 · CTD heptad position map

Slices the Rpb1 sequence into the CTD region (positions 1593–1970), partitions the heptad-bearing portion (1593-1960) into 52 Y-delimited heptapeptides, and maps each position to its `(heptad_index, position_within_heptad)` coordinates. Guards the heptad count (52) and mapped-residue count (368) with assertions.

### Module 9 · Cross-database alignment and literature-mined additions

Augments the six PTM database tables with a seventh `literature_mining` source covering:

- 51 proline cis/trans isomerization sites (Pro3: 26 sites, Pro6: 25 sites; Gibbs et al., 2017; Noble et al., 2005; Xiang et al., 2010)
- O-GlcNAcylation at S1829 and S1896 (Lewis et al., 2016)
- Citrullination at R1810 (Sharma et al., 2019)

Outer-joins all seven sources into a wide-form audit table `ptm_comparison`.

### Module 10 · PTM data curation and integration

Harmonizes all source nomenclature to a controlled PTM vocabulary, removes non-reversible PTM classes (N-Glycosylation, Caspase cleavage), resolves O-GlcNAcylation / O-Glycosylation overlaps, and writes the final `all_databases_merge` table to `ptms_polII_{CURRENT DATE}.tsv`. Subsequent runs detect this cache and skip all network access.

---

## Notebook 2 — Information-Theoretic Calculations

### Environment check and analysis setup (required)

> [!WARNING] **Running this cell before all Reproducibility Modules in Notebook 2 is REQUIRED**, since it generates essential assets for downstream analysis.

This cell verifies dependencies for the results notebook, then loads the cached sequence and PTM files, reconstructs the CTD heptad map, and partitions `all_databases_merge` into three separate dataframes ('df') used by all downstream modules:

<table>
<thead>
<tr>
<th>Frame</th>
<th>Content</th>
</tr>
</thead>
<tbody><tr>
<td>ctd_df</td>
<td>Rpb1 CTD, positions 1593–1970</td>
</tr>
<tr>
<td>core_df</td>
<td>Rpb1 non-CTD core, positions 1–1592</td>
</tr>
<tr>
<td>r212_df</td>
<td>Subunits Rpb2 – Rpb12</td>
</tr>
</tbody></table>

Also defines the information-capacity helper **_I(df) = Σ log2(n_PTM_types + 1)** where +1 accounts for the baseline PTM "unmodified" state, summed over unique `(Accession, Residue)` sites.

### Module 11 · Colour-coded CTD heptad map *(Table 1)*

Renders Table 1: an HTML map of all 52 CTD heptapeptides plus the 10-residue C-terminal tail, colour-coded by PTM combination. Heptads with >7 residues (heptad 2 and heptad 49) are flagged with an asterisk.

### Module 12 · CTD state space, information capacity, and residue-class decomposition *(Table 2)*

Computes:

- **N_CTD** = ∏ᵢ sᵢ (where sᵢ is the number of discrete modified states in residue *i*; log10 N_CTD = **63.58**)
- **I_CTD** = log2(N_CTD) = **211.22 bits**

Reproduces Table 2 with per-subclass site counts, sᵢ, log2(sᵢ), and total bit contributions.

### Module 13 · Rpb1 non-CTD core information capacity *(Supplementary Table S1)*

Applies `_I` to `core_df` and renders one row per modified residue with Residue, PTM type(s), States, and Bits per site.

### Module 14 · Rpb2–Rpb12 information capacities *(Supplementary Tables S2–S12)*

Applies the same procedure to each of the 11 non-Rpb1 subunits in Rpb2 → Rpb12 order and reports per-subunit information capacities.

### Module 15 · Per-molecule and per-nucleus information capacity

Sums all three compartments:

```
I_total = I_CTD + I_core + I_r212
I_nucleus = I_total × 80,200 Pol II copies/nucleus
```

Capacities are reported in bits, bytes, and megabytes (1 MB = 10^6 bytes).

### Module 16 · Multi-timescale PTM architecture and physiological information capacity

Partitions I_total into three regulatory layers. Layer site counts are physiologically curated constants derived from the literature (see manuscript Methods), not raw counts from the integrated atlas:

<table>
<thead>
<tr>
<th>Layer</th>
<th>Sites</th>
<th>States</th>
<th>Timescale</th>
</tr>
</thead>
<tbody><tr>
<td>Fast (phosphorylation)</td>
<td>30</td>
<td>5</td>
<td>~5 s – 15 min</td>
</tr>
<tr>
<td>Intermediate (Pro isomerization + O-GlcNAc)</td>
<td>32</td>
<td>2</td>
<td>~1 – 30 min</td>
</tr>
<tr>
<td>Slow (ubiquitination)</td>
<td>10</td>
<td>2.5</td>
<td>~15 min – 24 h</td>
</tr>
</tbody></table>

**I_physio = 114.88 bits/molecule · I_phys_nucleus = 1.15 MB**

### Module 17 · Total unique PTM sites *(Supplementary Information S1)*

Reports the total unique modified residues `(Accession, Residue)` and unique `(Accession, Residue, PTM)` triplets across the entire atlas.

### Module 18 · Sensitivity analyses *(Supplementary Information S3)*

Re-applies `_I` under four alternative state models and generates Supplementary Information S3-Table S1:

<table>
<thead>
<tr>
<th>Scenario</th>
<th>Description</th>
<th>I_PolII (bits per molecule)</th>
</tr>
</thead>
<tbody><tr>
<td>S0</td>
<td>Manuscript baseline</td>
<td>707.98</td>
</tr>
<tr>
<td>S1</td>
<td>No Thr-phosphorylation outside CTD</td>
<td>644.98</td>
</tr>
<tr>
<td>S2</td>
<td>Baseline non-CTD + no Pro cis/trans isomerization</td>
<td>656.98</td>
</tr>
<tr>
<td>S3</td>
<td>Maximally conservative (S1 + S2 + no CTD Thr-P, Lys-7, Arg mods)</td>
<td>544.44</td>
</tr>
</tbody></table>

---

## Data Sources

<table>
<thead><tr><th>Database</th><th>URL</th></tr></thead>
<tbody><tr><td>UniProt UniSave</td><td><a href="https://www.ebi.ac.uk/uniprot/unisave/app/#/">https://www.ebi.ac.uk/uniprot/unisave/app/#/</a></td></tr><tr><td>UniProtKB REST</td><td><a href="https://www.uniprot.org/uniprotkb/">https://www.uniprot.org/uniprotkb/</a></td></tr><tr><td>EBI Proteins API</td><td><a href="https://www.ebi.ac.uk/proteins/api/">https://www.ebi.ac.uk/proteins/api/</a></td></tr><tr><td>dbPTM</td><td><a href="https://biomics.lab.nycu.edu.tw/dbPTM/">https://biomics.lab.nycu.edu.tw/dbPTM/</a></td></tr><tr><td>iPTMnet</td><td><a href="https://research.bioinformatics.udel.edu/iptmnet/">https://research.bioinformatics.udel.edu/iptmnet/</a></td></tr><tr><td>PhosphoSitePlus</td><td><a href="https://www.phosphosite.org/">https://www.phosphosite.org/</a></td></tr><tr><td>GlyGen</td><td><a href="https://api.glygen.org/">https://glygen.org/</a></td></tr><tr><td>BioNumbers ID 112321; Zhao et al., 2014</td><td><a href="https://bionumbers.hms.harvard.edu/bionumber.aspx?s=n&v=0&id=112321">https://bionumbers.hms.harvard.edu/bionumber.aspx?s=n&v=0&id=112321</a></td></tr></tbody></table>

---

## Key Results

<table>
<thead>
<tr>
<th>Quantity</th>
<th>Value</th>
</tr>
</thead>
<tbody><tr>
<td>PTM databases integrated</td>
<td>6 + literature mining</td>
</tr>
<tr>
<td>Pol II subunits covered</td>
<td>12 (Rpb1–Rpb12)</td>
</tr>
<tr>
<td>CTD heptad repeats</td>
<td>52</td>
</tr>
<tr>
<td>log<sub>10</sub> N_CTD</td>
<td>63.58</td>
</tr>
<tr>
<td>I_CTD</td>
<td>211.22 bits</td>
</tr>
<tr>
<td>I_total (per molecule)</td>
<td>707.98 bits</td>
</tr>
<tr>
<td>I_nucleus (80,200 copies)</td>
<td>~7.10 MB*</td>
</tr>
<tr>
<td>I_physio (per molecule)</td>
<td>114.88 bits</td>
</tr>
<tr>
<td>I_phys_nucleus</td>
<td>~1.15 MB*</td>
</tr>
</tbody></table>

*1 MB = 10^6 bytes

---

## Authors

- **Jose Carrasco-Pujante** – [GitHub](https://github.com/JoseCarrascoPujante) | [ORCID](https://orcid.org/0000-0001-6490-738X)
- **Borja Camino-Pontes** – [GitHub](https://github.com/Dopert) | [ORCID](https://orcid.org/0000-0002-9071-9304)

---

## Citation

If you use this reproducibility package, please cite **both** the manuscript and the archived software/data package:

**Manuscript**

> De la Fuente, I.M., Carrasco-Pujante, J., Camino-Pontes, B., Fedetz, M., Legarreta, L.,  Malaina, I., Pérez-Yarza, G., Martínez, L., Cortés, J.M., and López, J.I. *Multi-timescale PTM architecture in human RNA polymerase II*

**Reproducibility package (this repository, archived on Zenodo)**

> Carrasco-Pujante, J. and Camino-Pontes, B. (2026). *Pol II PTM Information-Capacity — Reproducibility Package* (Version 1.0.0) [Software]. Zenodo. [https://doi.org/10.5281/zenodo.20345702](https://doi.org/10.5281/zenodo.20345702)

BibTeX:

```bibtex
@software{jose_carrasco_pujante_2026_20345703,
  author       = {Jose Carrasco Pujante},
  title        = {JoseCarrascoPujante/Multi-timescale_PTM_architectu
                   re_in_human_RNA_polymerase_II: v0.1.0
                  },
  month        = may,
  year         = 2026,
  publisher    = {Zenodo},
  version      = {v0.1.0},
  doi          = {10.5281/zenodo.20345703},
  url          = {https://doi.org/10.5281/zenodo.20345703},
}
```

---

## License

[![](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![](https://img.shields.io/badge/License-CC--BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

See the LICENSE file for terms of use.