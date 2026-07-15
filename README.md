

# Correlation of SARS-CoV-2 Variants in Wastewater and Clinical Samples using Freyja

This repository accompanies the manuscript:  
**"Correlation assessment of SARS-CoV-2 variants and their subvariants present in clinical and wastewater samples in Oregon, USA (February 2021 – February 2022) using the Freyja bioinformatics approach"**  

## 📌 Why this study was done
Clinical sequencing is essential for discovering and tracking variants, but it can be affected by testing availability, reporting delays, and sampling bias. **Wastewater surveillance** offers a complementary community-wide signal that captures infections from both symptomatic and asymptomatic individuals.

This study was conducted to:
- Evaluate whether wastewater sequencing analyzed with **Freyja** can resolve SARS-CoV-2 variants and subvariants with accuracy comparable to clinical sequencing.
- Compare Freyja’s performance to a prior **MLST-based** approach that had lower subvariant resolution.

## 🧪 Study Design (Summary)
- **Samples:** 24-hour composite wastewater influent samples from Oregon WWTPs (Feb 2021 – Feb 2022).
- **Clinical data:** SARS-CoV-2 genomes from Oregon submitted to GISAID in the same period.
- **Sequencing & analysis:** Illumina short-read sequencing; lineage deconvolution with **Freyja**.
- **Comparisons:** Relative abundances in wastewater vs. clinical were compared using **Spearman’s ρ** and **R²**.

## 🧬 Variant and Subvariant Classification (Operational Definition)
To assess concordance at multiple phylogenetic depths, we applied a pragmatic grouping that respects Pangolin hierarchy and Nextclade clades:

- **Clade level (Nextclade):** Delta divided into **21A**, **21I**, **21J**.
- **Level 1:** Pangolin lineages **with single period** (e.g., `AY.16`) → `Delta_21A_Level_1`.
- **Level 2:** Pangolin lineages **with double period** (e.g., `AY.16.1`) → `Delta_21A_Level_2`.

*Example:*  
`AY.16` → `Delta_21A_Level_1`  
`AY.16.1` → `Delta_21A_Level_2`

> **Note:** “Level 1/Level 2” an **operational definition** introduced here to test whether wastewater–clinical correlations persist at deeper sublineage resolution.

## 📊 Key Findings (Highlights)
- Strong wastewater–clinical agreement overall (**Spearman’s ρ = 0.875; R² = 0.946**).
- Freyja resolved **>200 Delta AY subvariants** across clades **21A/21I/21J**.
- Subclade-level correlations remained high (**ρ = 0.892–0.959; R² = 0.716–0.979**).

## 🔄 Workflow

### A) Overall (Variants)

             Wastewater Sequencing Samples
                        │
                        ▼
                    Freyja Run
                        │
                        ▼
               Preprocessing Output
                        │
                        ▼
           Variants Grouping (Wastewater)
                        │
                        ▼
            Master File (Wastewater) ─┐
                                      │
                                      ▼
                            Spearman Correlation
                                      ▲
                                      │
            Master File (GISAID) ─────┘
                        ▲
                        │
             Variants Grouping (GISAID)
                        ▲
                        │
                    GISAID Output


### B) Delta variants (Clade/Level Resolution)


             Wastewater Sequencing Samples
                       │
                       ▼
                 Freyja Run
                       │
                       ▼
            Preprocessing Output (Delta variants)
                       │
                       ▼
         Variants Grouping (Delta, Wastewater)
                       │
                       ▼
        Master File (Delta, Wastewater) ─┐
                                         │
                                         ▼
                               Spearman Correlation
                                         ▲
                                         │
        Master File (Delta, GISAID) ─────┘
                       ▲
                       │
         Variants Grouping (Delta, GISAID)
                       ▲
                       │
                    GISAID Output



## 📂 Repository Structure
- `Notebooks/` – Jupyter notebooks for post-Freyja processing (variant grouping, correlations, plotting).
- `README.md` 

## 🚀 Usage
These notebooks are a **research companion**. They demonstrate how to:
1) group Freyja outputs into variant/clade/level categories,  
2) aggregate across sites/epi-weeks, and  
3) compute wastewater–clinical concordance (Spearman’s ρ, R²).


