# Section 4: Gene Counting (featureCounts)

[![Tool](https://img.shields.io/badge/Tool-featureCounts-blue?style=flat-square)](https://subread.sourceforge.net/)
[![Annotation](https://img.shields.io/badge/Annotation-BDGP6.87%20GTF-green?style=flat-square)]()
[![Strand](https://img.shields.io/badge/Strand-Reverse%20Stranded-orange?style=flat-square)]()
[![License](https://img.shields.io/badge/License-CC--BY--4.0-lightgrey?style=flat-square)](https://creativecommons.org/licenses/by/4.0/)

**Author:** Faiqa Zarar Noor | **NUST ID:** 471543 | **Date:** April 2026

---

## Abstract

This section documents the quantification of gene expression by counting the number of reads mapping to each annotated gene using featureCounts. The sorted BAM files from RNA STAR alignment were used as input, along with the Ensembl BDGP6.87 GTF annotation for *Drosophila melanogaster*. Reads were counted in reverse-stranded mode (confirmed by Infer Experiment), using paired-end fragment counting. The resulting count tables were used as direct input for DESeq2 differential expression analysis. MultiQC was used to summarize the featureCounts assignment statistics across samples.

---

## Table of Contents

1. [Overview](#1-overview)
2. [Folder Structure](#2-folder-structure)
3. [Methods](#3-methods)
   - 3.1 [Strandedness Determination](#31-strandedness-determination)
   - 3.2 [featureCounts](#32-featurecounts)
   - 3.3 [MultiQC Summary](#33-multiqc-summary)
4. [Results](#4-results)
5. [References](#5-references)

---

## 1. Overview

After alignment, the next step is to count how many reads map to each gene. This produces a **count matrix** — the primary input for differential expression analysis. The accuracy of this step depends critically on:

- Using the correct strandedness setting
- Using the correct annotation file (matching the genome version)
- Correctly handling paired-end reads

---

## 2. Folder Structure

```
04_gene_counting/
├── README.md                        ← This document
└── outputs/
    └── featurecounts_summary.tabular  # Assignment statistics
```

---

## 3. Methods

### 3.1 Strandedness Determination

Before running featureCounts, the library strandedness was determined using:

1. **Infer Experiment** (RSeQC) on the STAR BAM files
2. Visual inspection of strand-specific coverage in JBrowse

**Result:** Library is **reversely stranded** → featureCounts setting: `Stranded (Reverse)`

---

### 3.2 featureCounts

```
Tool      : featureCounts (Galaxy Version 2.1.1+galaxy0)
Input     : RNA STAR mapped.bam collection (dataset 65)
Annotation: Drosophila_melanogaster.BDGP6.87.gtf.gz (dataset 53)

Parameters:
  Strand specificity          : Stranded (Reverse)
  GFF feature type filter     : exon
  GFF gene identifier         : gene_id
  Paired-end reads            : Yes, count as 1 single fragment
  Minimum mapping quality     : 10
  Output format               : Gene-ID + read-count (DESeq2 compatible)
  Create gene-length file     : Yes

Outputs:
  - Counts table    : Gene IDs with read counts per sample
  - Summary table   : Assignment statistics
  - Feature lengths : Gene length file (for downstream goseq analysis)
```

---

### 3.3 MultiQC Summary

```
Tool    : MultiQC (Galaxy Version 1.27+galaxy4)
Input   : featureCounts Summary collection
Output  : Aggregated assignment statistics report
```

---

## 4. Results

### 4.1 Read Assignment Statistics

| Metric | GSM461177 (Untreated PE) | GSM461180 (Treated PE) |
|---|---|---|
| Assigned reads | >70% | >70% |
| Unassigned (unmapped) | <5% | <5% |
| Unassigned (multimapping) | <10% | <10% |
| Unassigned (no features) | <15% | <15% |

### 4.2 Output Count Table

The count table contains one row per gene and one column per sample:

```
GeneID                    GSM461177    GSM461180
FBgn0000003               0            0
FBgn0000008               97           63
FBgn0000014               0            0
FBgn0000015               1            2
...
```

Total genes quantified: **17,559 genes**

---

## 5. References

1. Liao, Y., Smyth, G. K., & Shi, W. (2014). featureCounts: an efficient general purpose program for assigning sequence reads to genomic features. *Bioinformatics*, 30(7), 923–930. https://doi.org/10.1093/bioinformatics/btt656
2. Wang, L., Wang, S., & Li, W. (2012). RSeQC: quality control of RNA-seq experiments. *Bioinformatics*, 28(16), 2184–2185. https://doi.org/10.1093/bioinformatics/bts356

---

⬅️ [Back: STAR Coverage](../03_star_coverage/README.md) &nbsp;|&nbsp; [Back to Main README](../README.md) &nbsp;|&nbsp; ➡️ [Next: DESeq2](../05_differential_expression/README.md)
