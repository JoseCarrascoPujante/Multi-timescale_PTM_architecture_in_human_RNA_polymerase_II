# Human RNA Polymerase II PTM State-Space Size — Reproducibility Package

> [!IMPORTANT] **Fully reproducible computation of human RNA Polymerase II post-translational modification state-space size, from raw database downloads to every figure, table, and numeric result in the manuscript "Multi-Timescale PTM Architecture in Human RNA Polymerase II".**

[![](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![](https://img.shields.io/badge/License-CC--BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/) [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20345702.svg)](https://doi.org/10.5281/zenodo.20345702)

---

> [!IMPORTANT] The Zenodo badge above is a **concept DOI**. It always resolves to the most recent release, which may be newer than the manuscript version you are reading. Use the table below to locate the release that reproduces the numbers in your copy.

| Manuscript version | Git tag | Zenodo version DOI | TSV files' date stamp |
|---|---|---|---|
| bioRxiv v1 | [v0.1.0-pre](https://github.com/JoseCarrascoPujante/Multi-timescale_PTM_architecture_in_human_RNA_polymerase_II/tree/v0.1.0-pre) | [10.5281/zenodo.20635050](https://doi.org/10.5281/zenodo.20635050) | 2026_05_22|
| current | [v1.0.0](https://github.com/JoseCarrascoPujante/Multi-timescale_PTM_architecture_in_human_RNA_polymerase_II/tree/v0.1.0-pre) | [10.5281/zenodo.22139933](https://doi.org/10.5281/zenodo.22139933) | 2026_08_26 |

> A release's own version DOI does not exist until Zenodo publishes it, so the copy of this table archived inside such Zenodo release cannot contain it. If the cell above still reads "minted at release", you are reading the archived copy: the release's DOI is recorded on the `main` branch and on the [Zenodo record](https://doi.org/10.5281/zenodo.20345702), which always resolves to the newest release.

### Archival policy

- Every manuscript version is paired with a tagged GitHub release and a Zenodo version DOI.
- The `main` branch always tracks the most recent manuscript version, and may not reproduce the numbers in an earlier one.
- The bundled `.tsv` snapshots are date-stamped and both notebooks' audit assertions are pinned to the manuscript values of their own release, so running a release against a mismatched snapshot fails loudly rather than returning a silently incorrect result.

---

### Table of contents

- Overview
- Quick Start
- Requirements
- Repository Structure
- Notebook 1 — PTM and Protein Sequence Data Retrieval
- Notebook 2 — Logarithmic State-Space Calculations
- Data Sources
- Key Results
- Authors
- Citation
- License

---

## Overview

This repository contains two Jupyter notebooks: `notebook_1.ipynb` (Supplementary Data File 1) and `notebook_2.ipynb` (Supplementary Data File 2) that together reproduce the datasets used and the numeric values and tables reported in the main text and in Supplementary Information S2-S4. `notebook_1.ipynb` can additionally generate fresh versions of the two .tsv files provided together with the Jupyter notebooks in this repository: `uniprot_polII_seqs_2026_08_26.tsv` (Supplementary Data File 3) and ``ptms_polII_`2026_08_26`.tsv`` (Supplementary Data File 4), provided the retrieved upstream (online) databases did not change. The calculation integrates six public PTM databases plus literature-mined entries to build a comprehensive atlas of human RNA Polymerase II post-translational modifications, then applies information-theoretic methods to quantify the regulatory capacity encoded by those modifications.

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
<td>~8 min</td>
</tr>
<tr>
<td>notebook_2.ipynb</td>
<td>11 – 20</td>
<td>Logarithmic state-space calculations and all manuscript outputs</td>
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
<tbody><tr><td>uniprot_polII_seqs_2026_08_26.tsv</td><td>Version-pinned UniProt sequences (Supplementary Data File 3)</td></tr><tr><td>ptms_polII_2026_08_26.tsv</td><td>Frozen PTM atlas (Supplementary Data File 4)</td></tr></tbody></table>

When both files are present, every network download in Notebook 1 is automatically skipped and Notebook 2 reads directly from the snapshots. All audit assertions in both Notebooks are pinned to these snapshots.

### Full pipeline replication (network required)

Simply run Notebook 1 without the cached files. Each module will query its respective upstream database, the frozen Pol II subunit protein sequences will be written to `uniprot_polII_seqs_{CURRENT DATE}.tsv` and the frozen PTM atlas will be written to `ptms_polII_{CURRENT DATE}.tsv` for future offline runs.

> [!NOTE] The audit assertions in Notebook 2 are pinned to the bundled snapshots and **will fail** if run against a freshly regenerated atlas (upstream databases may have been updated).

---

## Requirements

- Notebook 1: pandas, requests, beautifulsoup4, IPython
- Notebook 2: pandas, IPython, jinja2

Install all dependencies:

```bash
pip install pandas requests beautifulsoup4 ipython jinja2
```

---

## Repository Structure

```
├── notebook_1.ipynb                      # Supplementary Data File 1 — Notebook 1: database downloads & atlas build
├── notebook_2.ipynb                      # Supplementary Data File 2 — Notebook 2: logarithmic state-space calculations
├── uniprot_polII_seqs_2026_08_26.tsv     # Supplementary Data File 3 — pinned protein sequences
├── ptms_polII_2026_08_26.tsv             # Supplementary Data File 4 — frozen PTM atlas
└── README.md                             # Reproducibility instructions
```

---

## Notebook 1 — PTM and Protein Sequence Data Retrieval

### Environment check (required)

Verifies all software dependencies and aborts with a diagnostic message if any requirement is missing or below the required version.

### Module 1 · Canonical UniProt sequence retrieval

Downloads the 12 human Pol II subunit sequences from UniProt UniSave with hard-coded version numbers, guaranteeing byte-identical output across runs. Writes `uniprot_polII_seqs_{CURRENT DATE}.tsv` if no cache is found.

**Pinned UniProt versions:**

<table>
<thead><tr><th>Accession</th><th>Version</th><th>Subunit</th></tr></thead>
<tbody><tr><td>P24928</td><td>249</td><td>Rpb1</td></tr><tr><td>P30876</td><td>232</td><td>Rpb2</td></tr><tr><td>P19387</td><td>236</td><td>Rpb3</td></tr><tr><td>O15514</td><td>212</td><td>Rpb4</td></tr><tr><td>P19388</td><td>238</td><td>Rpb5</td></tr><tr><td>P61218</td><td>188</td><td>Rpb6</td></tr><tr><td>P62487</td><td>179</td><td>Rpb7</td></tr><tr><td>P52434</td><td>231</td><td>Rpb8</td></tr><tr><td>P36954</td><td>222</td><td>Rpb9</td></tr><tr><td>P62875</td><td>188</td><td>Rpb10</td></tr><tr><td>P52435</td><td>216</td><td>Rpb11</td></tr><tr><td>P53803</td><td>210</td><td>Rpb12</td></tr></tbody></table>

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

## Notebook 2 — Logarithmic State-Space Calculations

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

Also defines the state-space size helper **_I(df) = Σ log2(n_PTM_types + 1)** where +1 accounts for the baseline PTM "unmodified" state, summed over unique `(Accession, Residue)` sites.

### Module 11 · Colour-coded CTD heptad map *(Table 1)*

Renders Table 1: an HTML map of all 52 CTD heptapeptides plus the 10-residue C-terminal tail, colour-coded by PTM combination. Heptads with >7 residues (heptad 2 and heptad 49) are flagged with an asterisk.

### Module 12 · CTD state-space size and residue-class decomposition *(Table 2)* plus per-phosphoacceptor-position contributions (Results IX and Figure 5B)

Computes:

- **N_CTD** = ∏ᵢ sᵢ (where sᵢ is the number of discrete modified states in residue *i*; log10 N_CTD = **63.58**)
- **I_CTD** = log2(N_CTD) = **211.22 bits**

Reproduces Table 2 with per-subclass site counts, sᵢ, log2(sᵢ), and total bit contributions.

It then reports the per-phosphoacceptor-position contributions (Tyr1, Ser2, Thr4, Ser5, Ser7) used in Results IX and Figure 5B, with a check pinning them to the values reported in the manuscript.

### Module 13 · Rpb1 non-CTD core state-space size *(Supplementary Table S1)*

Applies `_I` to `core_df` and renders one row per modified residue with Residue, PTM type(s), States, and Bits per site.

### Module 14 · Rpb2–Rpb12 state-space sizes *(Supplementary Tables S2–S12)*

Applies the same procedure to each of the 11 non-Rpb1 subunits in Rpb2 → Rpb12 order and reports per-subunit information capacities.

### Module 15 · Per-molecule and microstate logarithmic state-space size

Sums all three compartments:

```
I_total = I_CTD + I_core + I_r212
I_microstate = I_total × 80,200 Pol II copies/nucleus
```

Capacities are reported in bits and bytes.

### Module 16 · Multi-timescale PTM architecture and effective-state scenario size

Partitions I_total into three regulatory layers. Layer site counts are physiologically curated constants derived from the literature (see manuscript Methods), not raw counts from the integrated atlas:

<table style="width: 44.3084%;"><colgroup><col style="width: 57.8431%;"><col style="width: 9.21569%;"><col style="width: 10.9804%;"><col style="width: 23.6514%;"></colgroup>
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
<td>36</td>
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

**I_scenario = 118.88 bits/molecule**

### Module 17 · Total unique PTM annotations

Reports the total unique `(Accession, Residue, PTM)` triples across the entire atlas.

### Module 18 · Sensitivity analyses of PTM state-space size estimates (Supplementary Information S2)*

Re-applies `_I` under four alternative state models and generates Supplementary Information S2-Table S1:

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

### Module 19 · Sensitivity of the heuristic effective-state scenario *(Supplementary Information S3)*

Varies each parameter of the three-layer scenario one at a time while holding the others at their central values, and reports the resulting logarithmic state-space size. Renders Supplementary Information S3-Table S1 to S3-Table S7 and the local analytical sensitivities of S3.4.

<table>
<thead><tr><th>Varied</th><th>Range</th><th>I_scenario (bits)</th></tr></thead>
<tbody>
<tr><td>m_f, effective fast heptads</td><td>20 – 40</td><td>95.66 – 142.10</td></tr>
<tr><td>a_f, fast alphabet</td><td>2 – 5</td><td>79.22 – 118.88</td></tr>
<tr><td>m_i, intermediate processes</td><td>25 – 47</td><td>107.88 – 129.88</td></tr>
<tr><td>f_i, intermediate independence</td><td>0.25 – 1.00</td><td>91.88 – 118.88</td></tr>
<tr><td>m_s, slow processes</td><td>5 – 20</td><td>112.27 – 132.10</td></tr>
<tr><td>a_s, slow alphabet</td><td>2 – 3</td><td>115.66 – 121.51</td></tr>
</tbody></table>

Combined coarse-graining scenarios span **38.00 bits** (stronger) to **172.19 bits** (weaker) around the **118.88-bit** central parameterization. All values are deterministic sensitivity ranges, not confidence intervals or physiological bounds.

### Module 20 · Sensitivity to PTM annotation support *(Supplementary Information S4)*

Uses the seven `src_*` provenance columns of the frozen atlas to test how the estimate depends on cross-resource agreement. Database-support thresholds are applied **before** the residue alphabets are rebuilt, so removing an annotation correctly shrinks the composite state count at multi-PTM residues. Literature-defined states are retained at every threshold; a stricter all-channel variant is reported as a stress test. Renders Supplementary Information S4-Table S1 to S4-Table S5. Three audits guard the reported values: the unfiltered baseline, the seven per-source support totals of the deposited atlas, and the annotation counts retained at R ≥ 2 and R ≥ 3. A regenerated atlas that differs from the deposited snapshot therefore fails loudly rather than silently producing different Supplementary Information S4 numbers.

<table>
<thead><tr><th>Quantity</th><th>Value</th></tr></thead>
<tbody><tr><td>Retention at R ≥ 2</td><td>487.23 bits (Q₂ = 0.688)</td></tr><tr><td>Retention at R ≥ 3</td><td>399.21 bits (Q₃ = 0.564)</td></tr><tr><td>Largest leave-one-resource-out effect</td><td>dbPTM, ΔI = 140.91 bits</td></tr><tr><td>Resources contributing no or nearly no unique capacity</td><td>GlyGen (0.00), PhosphoSitePlus (0.26)</td></tr></tbody></table>

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
<td>~8 min</td>
</tr>
<tr>
<td>notebook_2.ipynb</td>
<td>11 – 20</td>
<td>Logarithmic state-space calculations and all manuscript outputs</td>
<td>~1 s</td>
</tr>
</tbody></table>0000

*1 MB = 10^6 bytes

---

## Authors

- **Jose Carrasco-Pujante** – [GitHub](https://github.com/JoseCarrascoPujante) | [ORCID](https://orcid.org/0000-0001-6490-738X) *Principal developer.*
- **Borja Camino-Pontes** – [GitHub](https://github.com/Dopert) | [ORCID](https://orcid.org/0000-0002-9071-9304)

---

## Citation

**Reproducibility package (this repository, archived on Zenodo)**

Cite the version you used:

> Carrasco-Pujante, J., & Camino-Pontes, B. (2026). *Reproducibility package for "Multi-timescale PTM architecture in human RNA polymerase II"* (v1.0.0) [Software]. Zenodo. [https://doi.org/10.5281/zenodo.22139933](https://doi.org/10.5281/zenodo.22139933)

To cite the package as an evolving artifact across all versions, use the concept DOI [https://doi.org/10.5281/zenodo.20345702](https://doi.org/10.5281/zenodo.20345702), which always resolves to the latest release.

---

## License

[![](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![](https://img.shields.io/badge/License-CC--BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

See the LICENSE file for terms of use.