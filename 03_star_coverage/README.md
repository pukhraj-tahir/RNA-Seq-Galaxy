# Section 3: STAR Coverage Strands

[![Tool](https://img.shields.io/badge/Tool-bamCoverage%20%7C%20JBrowse-blue?style=flat-square)](https://deeptools.readthedocs.io/)
[![Strand1](https://img.shields.io/badge/Strand%201-Blue-blue?style=flat-square)]()
[![Strand2](https://img.shields.io/badge/Strand%202-Red-red?style=flat-square)]()
[![License](https://img.shields.io/badge/License-CC--BY--4.0-lightgrey?style=flat-square)](https://creativecommons.org/licenses/by/4.0/)

**Author:** Faiqa Zarar Noor | **NUST ID:** 471543 | **Date:** April 2026

---

## Abstract

This section documents the strand-specific coverage visualization of RNA STAR alignment results. Strand-specific coverage is critical for determining library strandedness and for visualizing which genes are expressed on which strand. Using bamCoverage from the deepTools suite, separate bigWig coverage files were generated for the forward (strand 1) and reverse (strand 2) strands. These were visualized in JBrowse with strand 1 displayed in **blue** and strand 2 displayed in **red**. The predominance of signal on strand 2 (reverse) confirmed that the library is **reversely stranded**, consistent with the dUTP-based library preparation protocol used in this study.

---

## Table of Contents

1. [Overview](#1-overview)
2. [Folder Structure](#2-folder-structure)
3. [Methods](#3-methods)
   - 3.1 [bamCoverage — Forward Strand](#31-bamcoverage--forward-strand)
   - 3.2 [bamCoverage — Reverse Strand](#32-bamcoverage--reverse-strand)
   - 3.3 [JBrowse Visualization](#33-jbrowse-visualization)
4. [Results](#4-results)
5. [References](#5-references)

---

## 1. Overview

Library strandedness is a critical parameter in RNA-Seq analysis. It determines:
- Which strand reads are counted from in featureCounts
- How to interpret overlapping genes on opposite strands
- The accuracy of differential expression results

This section uses strand-specific coverage tracks to visually confirm the library strandedness.

---

## 2. Folder Structure

```
03_star_coverage/
├── README.md                                  ← This document
└── outputs/
    └── jbrowse_strand1_blue_strand2_red.png   # Strand coverage visualization
```

---

## 3. Methods

### 3.1 bamCoverage — Forward Strand (Strand 1 — Blue)

```
Tool      : bamCoverage (Galaxy Version 3.5.4+galaxy0)
Input     : RNA STAR mapped.bam collection
Parameters:
  Bin size              : 50
  Effective genome size : dm6 (142,573,017)
  Normalization         : Normalize to 1x
  Strand filter         : forward
  Min mapping quality   : 1
Output    : Forward strand bigWig file
Color     : Blue (in JBrowse visualization)
```

---

### 3.2 bamCoverage — Reverse Strand (Strand 2 — Red)

```
Tool      : bamCoverage (Galaxy Version 3.5.4+galaxy0)
Input     : RNA STAR mapped.bam collection
Parameters:
  Bin size              : 50
  Effective genome size : dm6 (142,573,017)
  Normalization         : Normalize to 1x
  Strand filter         : reverse
  Min mapping quality   : 1
Output    : Reverse strand bigWig file
Color     : Red (in JBrowse visualization)
```

---

### 3.3 JBrowse Visualization

```
Tool      : JBrowse (Galaxy Version 1.16.11+galaxy1)
Reference : Fly (Drosophila melanogaster): dm6 Full
Region    : chr4:540,000-560,000

Tracks:
  Track 1 : BigWig XY — Forward strand (blue)
             pos_color = blue | neg_color = blue
  Track 2 : BigWig XY — Reverse strand (red)
             pos_color = red  | neg_color = red
```

Both tracks were enabled for both samples:
- GSM461177_untreat_paired (forward + reverse)
- GSM461180_treat_paired (forward + reverse)

---

## 4. Results

### 4.1 Strand Coverage Visualization

![Strand Coverage — Strand 1 Blue, Strand 2 Red](outputs/jbrowse_strand1_blue_strand2_red.png)

The JBrowse visualization clearly shows:
- 🔵 **Blue tracks** = Strand 1 (forward strand coverage)
- 🔴 **Red tracks** = Strand 2 (reverse strand coverage)

For both samples (GSM461177_untreat_paired and GSM461180_treat_paired), the signal is predominantly on the **reverse strand (red)**, confirming that the library is **reversely stranded**.

### 4.2 Strandedness Conclusion

| Method | Result |
|---|---|
| Visual inspection (JBrowse strands) | Reverse stranded |
| Infer Experiment (RSeQC) | Reverse stranded |
| featureCounts setting used | Stranded (Reverse) |

---

## 5. References

1. Ramírez, F., et al. (2016). deepTools2: a next generation web server for deep-sequencing data analysis. *Nucleic Acids Research*, 44(W1), W160–W165. https://doi.org/10.1093/nar/gkw257
2. Levin, J. Z., et al. (2010). Comprehensive comparative analysis of strand-specific RNA sequencing methods. *Nature Methods*, 7, 709–715. https://doi.org/10.1038/nmeth.1491

---

⬅️ [Back: Alignment](../02_alignment/README.md) &nbsp;|&nbsp; [Back to Main README](../README.md) &nbsp;|&nbsp; ➡️ [Next: Gene Counting](../04_gene_counting/README.md)
