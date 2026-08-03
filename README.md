# Correlation of SARS-CoV-2 Variants in Wastewater and Clinical Samples using Freyja

This repository accompanies the manuscript:

**"Correlation assessment of SARS-CoV-2 variants and their subvariants present in clinical and wastewater samples in Oregon, USA (February 7, 2021 – February 26, 2022) using the Freyja bioinformatics approach"**

---

## Why this study was done

Clinical sequencing is essential for discovering and tracking variants, but it is affected by testing availability, reporting delays, and sampling bias. Wastewater surveillance offers a complementary community-wide signal that captures infections from both symptomatic and asymptomatic individuals.

This study was conducted to:

- Evaluate whether wastewater sequencing analysed with **Freyja** resolves SARS-CoV-2 variants and subvariants in agreement with clinical surveillance.
- Compare Freyja's performance against a prior **MLST-based** approach that had lower subvariant resolution.

Note that the study assesses **agreement** between wastewater and clinical surveillance. Because both data streams are observational, agreement between them demonstrates concordance rather than independent validation of lineage deconvolution.

---

## Study design

- **Wastewater samples:** 24-hour composite influent samples from up to 43 Oregon wastewater treatment facilities, collected at least weekly from February 2021 to February 2022 (55 weeks).
- **Clinical data:** SARS-CoV-2 genomes from Oregon submitted to GISAID over the same period (EPI_SET_260715qr).
- **Sequencing and analysis:** Illumina short-read sequencing; lineage deconvolution with Freyja v1.4.5 using the UShER barcode library dated 31 July 2023.
- **Comparison:** Relative abundances in wastewater and clinical data compared using Spearman's rank correlation coefficient (rs), with 95% confidence intervals obtained by circular block bootstrap.

---

## Statistical approach

Weekly observations within an epidemic wave are serially dependent, so p-values computed under an assumption of independence are not interpretable. This analysis therefore reports **no p-values**.

Instead, each Spearman rs is accompanied by a 95% confidence interval from a **circular block bootstrap**:

| Parameter | Value |
|---|---|
| Block length | 4 consecutive weeks |
| Justification | n^(1/3) rule, n = 55 weeks (Hall, Horowitz & Jing, 1995) |
| Replicates | 2,000 |
| Method | Percentile |
| Random seed | 20260724 (fixed) |

Because the seed is fixed, every published interval is exactly reproducible from this code.

Confidence intervals at block lengths of 2 to 6 weeks are provided as supporting information in the manuscript.

No coefficient of determination is reported. R² assumes a linear relationship and homoscedastic errors, neither of which is appropriate for compositional proportions bounded between 0 and 1, and it is inconsistent with a rank-based correlation. The correlation panels of Figures 3, 4 and 5 show an identity line at y = x rather than a fitted regression line, with both axes on a common scale within each panel.

---

## Variant and subvariant classification

To assess agreement at multiple phylogenetic depths, an operational grouping was applied that follows the Pango lineage hierarchy within Nextclade clades:

- **Clade level (Nextclade):** Delta divided into **21A**, **21I**, **21J**
- **Level 1:** Pango lineages with a single period, e.g. `AY.16` → `Delta_21A_Level_1`
- **Level 2:** Pango lineages with a double period, e.g. `AY.16.1` → `Delta_21A_Level_2`

> **Note:** "Level 1" and "Level 2" are operational definitions introduced in this study to test whether wastewater–clinical agreement persists at deeper sublineage resolution. They are not standard nomenclature.

Lineage names follow the **Pango nomenclature**. The Pangolin software was not run in this study; lineage assignments were produced directly by Freyja. Lineages were grouped into World Health Organization variant designations using a mapping retrieved in January 2024 from the Nextclade SARS-CoV-2 dataset, and that mapping file is included in this repository.

---

## Key findings

- Wastewater and clinical relative abundances showed strong agreement overall (rs = 0.875, 95% CI 0.813 – 0.918).
- Freyja resolved more than 200 Delta AY subvariants across clades 21A, 21I and 21J.
- Agreement remained strong at the clade and Level 1 resolution (rs = 0.892 – 0.959).
- Agreement at Level 2 was inconsistent. One comparison, Delta 21I Level 2, produced a confidence interval that includes zero (rs = 0.324, 95% CI −0.074 to 0.699) and is not interpreted as evidence of agreement.
- Level 3 subvariants were detected in wastewater, but agreement at that resolution could not be assessed because corresponding clinical data were unavailable.

---

## Workflow

### A) Overall variants

```
         Wastewater sequencing samples
                     |
                     v
                 Freyja run
                     |
                     v
            Preprocessing output
                     |
                     v
       Variant grouping (wastewater)
                     |
                     v
        Master file (wastewater) ----+
                                     |
                                     v
                        Spearman correlation
                            + bootstrap CI
                                     ^
                                     |
        Master file (GISAID) --------+
                     ^
                     |
         Variant grouping (GISAID)
                     ^
                     |
                GISAID output
```

### B) Delta subvariants (clade and level resolution)

```
         Wastewater sequencing samples
                     |
                     v
                 Freyja run
                     |
                     v
     Preprocessing output (Delta subvariants)
                     |
                     v
    Variant grouping (Delta, wastewater)
                     |
                     v
     Master file (Delta, wastewater) ---+
                                        |
                                        v
                           Spearman correlation
                               + bootstrap CI
                                        ^
                                        |
     Master file (Delta, GISAID) -------+
                     ^
                     |
      Variant grouping (Delta, GISAID)
                     ^
                     |
                GISAID output
```

---

## Repository structure

```
Freyja_Correlation_Study/
├── Freyja_Manuscript/
│   ├── Manuscript_Code_File_All_Variants.ipynb   Analysis notebook
│   └── Master_File/
│       ├── Lineages_Relative_Abundance/          Freyja output, all variants
│       ├── Delta_subvariants/                    Freyja output, Delta subvariants
│       ├── Gisaid_Data/                          Clinical summary data
│       ├── MLST/                                 MLST wastewater data
│       ├── Pango_WHO_mapping/                   Pango lineage to WHO mapping
│       └── Output_08012026/                      Generated results
│           ├── Combined_relative_abundance/
│           ├── FreyjaVSMLST/
│           ├── Delta_subvariants/
│           ├── Figures/                          Fig3, Fig4, Fig5
│           └── Tables/                           Tables 1, 2, 3
├── requirements.txt
├── LICENSE
└── README.md
```

---

## Running the analysis

The notebook was developed in **Google Colab** and reads its input files from Google Drive.

1. Copy the `Freyja_Manuscript` folder to your Google Drive.
2. Open `Manuscript_Code_File_All_Variants.ipynb` in Colab.
3. Edit `BASE_PATH` in the settings cell to point at your copy of `Master_File`.
4. Run the notebook **from top to bottom**.

Step 4 matters. The bootstrap section reads intermediate files that earlier cells write, so running cells out of order will raise `FileNotFoundError`.

All outputs are written to `Master_File/Output_08012026/`. Nothing outside that directory is modified.

The first two cells install Arial fonts and require a runtime restart. If typeface is not a concern, start from the third cell.

### Dependencies

Package versions are pinned in `requirements.txt`. Most ship with Colab by default; `arch`, which provides the circular block bootstrap, is installed by the notebook at runtime.

---

## Data availability

- **Raw sequencing reads:** NCBI BioProject [PRJNA938474](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA938474)
- **Clinical sequences:** GISAID, [https://doi.org/10.55876/gis8.260715qr](https://doi.org/10.55876/gis8.260715qr) (EPI_SET_260715qr). These are third-party data and cannot be redistributed here; access requires GISAID registration.
- **Code and generated results:** this repository

---

## License

MIT. See `LICENSE`.
