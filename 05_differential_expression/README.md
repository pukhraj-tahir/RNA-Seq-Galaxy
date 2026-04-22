# Section 5: Differential Gene Expression (DESeq2)

[![Tool](https://img.shields.io/badge/Tool-DESeq2-blue?style=flat-square)](https://bioconductor.org/packages/release/bioc/html/DESeq2.html)
[![Samples](https://img.shields.io/badge/Samples-7%20(3%20treated%20%2B%204%20untreated)-green?style=flat-square)]()
[![Factors](https://img.shields.io/badge/Factors-Treatment%20%7C%20Sequencing-orange?style=flat-square)]()
[![License](https://img.shields.io/badge/License-CC--BY--4.0-lightgrey?style=flat-square)](https://creativecommons.org/licenses/by/4.0/)

**Author:** Faiqa Zarar Noor | **NUST ID:** 471543 | **Date:** April 2026

---

## Abstract

This section documents the differential gene expression (DGE) analysis performed using DESeq2 on 7 *Drosophila melanogaster* RNA-Seq samples (3 treated with Pasilla RNAi knockdown and 4 untreated controls). A multi-factor design was used, incorporating both Treatment (treated vs untreated) and Sequencing type (paired-end vs single-end) as factors. DESeq2 performed median-of-ratios normalization and negative binomial dispersion estimation, producing: a PCA plot showing clear separation between treated and untreated samples along PC1 (48% variance), a sample-to-sample distance heatmap confirming expected clustering, an MA plot, and a p-value histogram. The Pasilla gene (FBgn0261552) was confirmed as significantly downregulated in treated samples.

---

## Table of Contents

1. [Overview](#1-overview)
2. [Folder Structure](#2-folder-structure)
3. [Experimental Design](#3-experimental-design)
4. [Methods](#4-methods)
   - 4.1 [Count File Import](#41-count-file-import)
   - 4.2 [DESeq2 Parameters](#42-deseq2-parameters)
   - 4.3 [DESeq2 Outputs](#43-deseq2-outputs)
5. [Results](#5-results)
   - 5.1 [PCA Plot](#51-pca-plot)
   - 5.2 [Sample-to-Sample Distance Heatmap](#52-sample-to-sample-distance-heatmap)
   - 5.3 [MA Plot](#53-ma-plot)
   - 5.4 [Differentially Expressed Genes](#54-differentially-expressed-genes)
6. [References](#6-references)

---

## 1. Overview

Differential gene expression analysis identifies genes whose expression levels differ significantly between conditions. DESeq2 is the gold-standard tool for this analysis because:

- It uses **negative binomial distribution** to model count data
- It performs **median-of-ratios normalization** accounting for library size and composition
- It **borrows information across genes** for dispersion estimation (especially important with few replicates)
- It controls the **false discovery rate** using the Benjamini-Hochberg procedure

---

## 2. Folder Structure

```
05_differential_expression/
├── README.md                          ← This document
└── outputs/
    ├── deseq2_plots.pdf               # PCA, heatmap, MA plot, dispersion
    ├── deseq2_results.tabular         # Full DGE results table
    └── deseq2_normalized_counts.tabular  # Normalized count matrix
```

---

## 3. Experimental Design

| Sample | Condition | Sequencing | Factor Assignment |
|---|---|---|---|
| GSM461176 | Untreated | Single-end | Untreated / SE |
| GSM461177 | Untreated | Paired-end | Untreated / PE |
| GSM461178 | Untreated | Paired-end | Untreated / PE |
| GSM461179 | Treated | Single-end | Treated / SE |
| GSM461180 | Treated | Paired-end | Treated / PE |
| GSM461181 | Treated | Paired-end | Treated / PE |
| GSM461182 | Untreated | Single-end | Untreated / SE |

**Primary factor:** Treatment (Treated vs Untreated)
**Secondary factor:** Sequencing type (PE vs SE)

---

## 4. Methods

### 4.1 Count File Import

Seven pre-computed featureCounts output files were imported from Zenodo:

```
https://zenodo.org/record/6457007/files/GSM461176_untreat_single_featureCounts.counts
https://zenodo.org/record/6457007/files/GSM461177_untreat_paired_featureCounts.counts
https://zenodo.org/record/6457007/files/GSM461178_untreat_paired_featureCounts.counts
https://zenodo.org/record/6457007/files/GSM461179_treat_single_featureCounts.counts
https://zenodo.org/record/6457007/files/GSM461180_treat_paired_featureCounts.counts
https://zenodo.org/record/6457007/files/GSM461181_treat_paired_featureCounts.counts
https://zenodo.org/record/6457007/files/GSM461182_untreat_single_featureCounts.counts
```

---

### 4.2 DESeq2 Parameters

```
Tool      : DESeq2 (Galaxy Version 2.11.40.8+galaxy2)
Input     : 7 featureCounts tabular files

Factor 1  : Treatment
  Level 1 : treated   → GSM461179, GSM461180, GSM461181
  Level 2 : untreated → GSM461176, GSM461177, GSM461178, GSM461182

Factor 2  : Sequencing
  Level 1 : PE → GSM461177, GSM461178, GSM461180, GSM461181
  Level 2 : SE → GSM461176, GSM461179, GSM461182

Files have header       : Yes
Use beta priors         : Yes
Output normalized counts: Yes
Generate plots          : Yes
Alpha (MA plot)         : 0.1
```

---

### 4.3 DESeq2 Outputs

DESeq2 produced 3 output files:

| Output | Description |
|---|---|
| Result file | Gene ID, mean counts, log2FC, p-value, adjusted p-value |
| Normalized counts | VST-normalized count matrix for all 7 samples |
| Plots PDF | PCA, heatmap, dispersion, p-value histogram, MA plot |

---

## 5. Results

### 5.1 PCA Plot

The PCA plot shows the 7 samples in the 2D plane of PC1 and PC2:

- **PC1 (48% variance)** separates **treated vs untreated** samples
- **PC2 (33% variance)** separates **paired-end vs single-end** samples
- No unexpected batch effects observed

| Group | PC1 | PC2 |
|---|---|---|
| PE Treated | Positive | Positive |
| PE Untreated | Negative | Positive |
| SE Treated | Positive | Negative |
| SE Untreated | Negative | Negative |

---

### 5.2 Sample-to-Sample Distance Heatmap

The heatmap of sample-to-sample distances shows:
- **Treated samples** cluster together (shorter distances)
- **Untreated samples** cluster together (shorter distances)
- Clear separation between treated and untreated groups
- Secondary clustering by sequencing type (PE vs SE)

Dark blue = closer samples (more similar expression profiles)

---

### 5.3 MA Plot

The MA plot displays:
- X-axis: Mean normalized counts (average expression strength)
- Y-axis: Log2 fold change
- **Blue points**: Genes with adjusted p-value < 0.1 (significantly DE)
- Majority of genes have log2FC near 0 (not differentially expressed)
- Several genes show significant up- or down-regulation

---

### 5.4 Differentially Expressed Genes

| Metric | Value |
|---|---|
| Total genes tested | ~17,559 |
| Significantly DE (adj p < 0.05) | ~1,000+ |
| Significantly DE with \|log2FC\| > 1 | ~113 |
| Pasilla gene (FBgn0261552) | Significantly downregulated ✅ |

---

## 6. References

1. Love, M. I., Huber, W., & Anders, S. (2014). Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. *Genome Biology*, 15, 550. https://doi.org/10.1186/s13059-014-0550-8
2. Brooks, A. N., et al. (2011). Conservation of an RNA regulatory map between Drosophila and mammals. *Genome Research*, 21(2), 193–202. https://doi.org/10.1101/gr.108662.110
3. Benjamini, Y., & Hochberg, Y. (1995). Controlling the false discovery rate: a practical and powerful approach to multiple testing. *Journal of the Royal Statistical Society*, 57(1), 289–300.

---

⬅️ [Back: Gene Counting](../04_gene_counting/README.md) &nbsp;|&nbsp; [Back to Main README](../README.md)
