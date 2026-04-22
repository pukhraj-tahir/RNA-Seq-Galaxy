# Section 1: Quality Control

[![Tool](https://img.shields.io/badge/Tool-Falco%20%7C%20MultiQC-blue?style=flat-square)](https://github.com/falco-project/falco)
[![Trimming](https://img.shields.io/badge/Trimming-Cutadapt-green?style=flat-square)](https://cutadapt.readthedocs.io/)
[![Reads](https://img.shields.io/badge/Reads-Paired--end%20%7C%20Single--end-orange?style=flat-square)]()
[![License](https://img.shields.io/badge/License-CC--BY--4.0-lightgrey?style=flat-square)](https://creativecommons.org/licenses/by/4.0/)

**Author:** Faiqa Zarar Noor | **NUST ID:** 471543 | **Date:** April 2026

---

## Abstract

This section documents the quality control and trimming steps applied to raw RNA-Seq FASTQ reads from *Drosophila melanogaster*. Raw reads were assessed using Falco (a fast FastQC alternative) and MultiQC. Adapter sequences and low-quality bases were removed using Cutadapt, followed by a second round of QC to confirm improvement in read quality. Both paired-end and single-end datasets passed QC with high per-base quality scores (Phred > 28) and acceptable adapter content after trimming.

---

## Table of Contents

1. [Overview](#1-overview)
2. [Folder Structure](#2-folder-structure)
3. [Methods](#3-methods)
   - 3.1 [Raw Read QC — Falco](#31-raw-read-qc--falco)
   - 3.2 [MultiQC Summary](#32-multiqc-summary)
   - 3.3 [Adapter Trimming — Cutadapt](#33-adapter-trimming--cutadapt)
   - 3.4 [Post-trimming QC](#34-post-trimming-qc)
4. [Results](#4-results)
5. [References](#5-references)

---

## 1. Overview

Quality control is the first and essential step in any RNA-Seq analysis. Poor quality reads or adapter contamination can lead to incorrect alignments and false differential expression results. This section covers:

- Assessment of raw read quality using **Falco**
- Aggregation of QC reports using **MultiQC**
- Adapter trimming using **Cutadapt**
- Post-trimming quality verification

---

## 2. Folder Structure

```
01_quality_control/
├── README.md                          ← This document
└── outputs/
    ├── falco_webpage.html             # Falco QC report (raw reads)
    ├── multiqc_raw_reads_report.html  # MultiQC before trimming
    └── multiqc_after_trimming_report.html  # MultiQC after trimming
```

---

## 3. Methods

### 3.1 Raw Read QC — Falco

```
Tool    : Falco (Galaxy Version 1.2.4+galaxy0)
Input   : 2 PE fastq pairs (GSM461177 + GSM461180)
Output  : HTML QC reports + RawData text files
```

Falco assesses:
- Per-base sequence quality
- Per-sequence quality scores
- Sequence duplication levels
- Adapter content
- GC content distribution

---

### 3.2 MultiQC Summary

```
Tool    : MultiQC (Galaxy Version 1.27+galaxy4)
Input   : Falco RawData output collection
Output  : Aggregated HTML report + Stats
```

MultiQC combines individual Falco reports into a single interactive report for easy comparison across samples.

---

### 3.3 Adapter Trimming — Cutadapt

```
Tool      : Cutadapt (Galaxy Version 4.4+galaxy0)
Input     : 2 PE fastq pairs
Mode      : Paired-end
Adapter   : Universal Illumina adapter (auto-detected)
Parameters:
  Minimum read length : 20
  Quality cutoff      : 20
Output    : Trimmed reads + trimming report
```

---

### 3.4 Post-trimming QC

After trimming, Falco and MultiQC were re-run on the trimmed reads to confirm:
- Removal of adapter sequences
- Improvement in per-base quality scores
- No significant loss of reads

---

## 4. Results

### 4.1 Raw Read Quality

| Metric | GSM461177 (Untreated PE) | GSM461180 (Treated PE) |
|---|---|---|
| Total reads | ~14M | ~12M |
| Mean quality score | >28 | >28 |
| Adapter content | Low | Low |
| GC content | ~48% | ~48% |

### 4.2 MultiQC Report — Before Trimming

![MultiQC Raw Reads](outputs/multiqc_raw_reads_report.html)

### 4.3 MultiQC Report — After Trimming

| Metric | Before Trimming | After Trimming |
|---|---|---|
| Reads with adapters | ~15% | ~0% |
| Mean quality | >28 | >30 |
| Reads retained | 100% | >95% |

---

## 5. References

1. De Sena Brandine, G., & Smith, A. D. (2021). Falco: high-speed FastQC emulation for quality control of sequencing data. *F1000Research*, 8, 1874. https://doi.org/10.12688/f1000research.21142.2
2. Ewels, P., et al. (2016). MultiQC. *Bioinformatics*, 32(19), 3047–3048. https://doi.org/10.1093/bioinformatics/btw354
3. Martin, M. (2011). Cutadapt removes adapter sequences from high-throughput sequencing reads. *EMBnet.journal*, 17(1), 10–12. https://doi.org/10.14806/ej.17.1.200

---

⬅️ [Back to Main README](../README.md) &nbsp;|&nbsp; ➡️ [Next: Alignment](../02_alignment/README.md)
