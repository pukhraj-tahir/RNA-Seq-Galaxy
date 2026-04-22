# 🧬 Reference-Based RNA-Seq Data Analysis on Galaxy

![Platform](https://img.shields.io/badge/Platform-Galaxy%20Europe-blue)
![Organism](https://img.shields.io/badge/Organism-Drosophila%20melanogaster-orange)
![Genome](https://img.shields.io/badge/Genome-dm6-green)
![Pipeline](https://img.shields.io/badge/Pipeline-RNA--Seq-purple)
![License](https://img.shields.io/badge/License-Academic-lightgrey)

---

| Field | Details |
|-------|---------|
| **Author** | Pukhraj Tahir |
| **Student ID** | 467407 |
| **Institution** | NUST — National University of Sciences and Technology |
| **Course** | BI-436: Special Topics in Bioinformatics |
| **Semester** | Spring 2026 |
| **Date** | April 2026 |

---

## 📄 Abstract

This repository documents a **complete reference-based RNA-Seq data analysis pipeline** executed on [Galaxy Europe](https://usegalaxy.eu/), following the [Galaxy Training Network tutorial](https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html).

The dataset consists of paired-end and single-end Illumina RNA-Seq reads from *Drosophila melanogaster*, comparing **treated** (Pasilla gene knockdown via RNAi) versus **untreated** conditions. The Pasilla (*ps*) gene encodes an RNA-binding protein that regulates splicing — its depletion mimics the loss of Nova1/Nova2 in humans, making it a biologically relevant model for studying splicing regulation and neurological disease.

The pipeline covers five major stages:
1. **Quality Control** — using Falco and MultiQC
2. **Read Trimming** — adapter removal with Cutadapt
3. **Spliced Alignment** — mapping to dm6 reference genome using RNA STAR, with visualization in IGV, JBrowse2, and strand-specific coverage plots
4. **Gene-Level Read Counting** — using featureCounts
5. **Differential Gene Expression Analysis** — using DESeq2, producing PCA plots, sample-to-sample distance heatmaps, MA plots, and normalized count tables

A total of **7 samples** (3 treated, 4 untreated) were analyzed. Results confirm successful knockdown of the Pasilla gene and reveal broader downstream transcriptomic changes consistent with its known role in splicing regulation.

**Keywords:** RNA-Seq · Differential Gene Expression · DESeq2 · STAR · featureCounts · *Drosophila melanogaster* · Pasilla · Galaxy · Spliced Alignment · PCA · Heatmap

---

## 📁 Table of Contents

1. [Repository Structure](#1-repository-structure)
2. [Biological Background](#2-biological-background)
3. [Dataset](#3-dataset)
4. [Pipeline Overview](#4-pipeline-overview)
5. [Section 1 — Quality Control](#5-section-1--quality-control)
6. [Section 2 — Read Trimming](#6-section-2--read-trimming)
7. [Section 3 — Alignment & Visualization](#7-section-3--alignment--visualization)
8. [Section 4 — STAR Strand Coverage](#8-section-4--star-strand-coverage)
9. [Section 5 — Gene Counting](#9-section-5--gene-counting)
10. [Section 6 — Differential Expression Analysis](#10-section-6--differential-expression-analysis)
11. [Key Results Summary](#11-key-results-summary)
12. [Tools & Software](#12-tools--software)
13. [How to Reproduce](#13-how-to-reproduce)
14. [References](#14-references)

---

## 1. Repository Structure

```
RNA-Seq-Galaxy-Tutorial/
│
├── README.md                                     ← This document
│
├── 01_quality_control/
│   ├── README.md                                 ← QC methods and results
│   └── outputs/
│       ├── multiqc_raw_reads_report.html         ← Pre-trimming QC summary
│       ├── multiqc_after_trimming_report.html    ← Post-trimming QC summary
│       └── falco_webpage.html                    ← Per-sample Falco report
│
├── 02_alignment/
│   ├── README.md                                 ← Alignment methods and results
│   └── outputs/
│       ├── jbrowse2_alignments_chr4.png          ← Read coverage visualization
│       └── multiqc_star_alignment_report.html    ← Alignment statistics summary
│
├── 03_star_coverage/
│   ├── README.md                                 ← STAR strand coverage description
│   └── outputs/
│       └── jbrowse_strand1_blue_strand2_red.png  ← Strand-specific coverage plot
│
├── 04_gene_counting/
│   ├── README.md                                 ← featureCounts methods and results
│   └── outputs/
│       └── featurecounts_summary.tabular         ← Per-sample count summaries
│
└── 05_differential_expression/
    ├── README.md                                 ← DESeq2 methods and results
    └── outputs/
        ├── deseq2_plots.pdf                      ← PCA, heatmap, MA plot
        ├── deseq2_results.tabular                ← Full DEG table with p-values
        └── deseq2_normalized_counts.tabular      ← Normalized expression counts
```

---

## 2. Biological Background

### What is RNA-Seq?
RNA sequencing (RNA-Seq) is a high-throughput sequencing technique used to quantify gene expression across the **entire transcriptome**. Unlike microarrays, RNA-Seq:
- Captures a wide dynamic range of expression levels
- Detects novel transcripts and splice variants
- Requires no prior knowledge of expressed sequences

### The Pasilla Gene Model
The **Pasilla (ps)** gene in *Drosophila melanogaster* encodes a nuclear RNA-binding protein of the NOVA family. It regulates alternative splicing of hundreds of target genes. This experiment models the effect of **ps knockdown via RNAi** on the transcriptome, and is a well-established model system for studying splicing regulation relevant to human Nova1/Nova2 proteins, which are associated with paraneoplastic neurological disorders.

### Why Reference-Based RNA-Seq?
In reference-based RNA-Seq, reads are mapped against a **known reference genome** (dm6) rather than assembled de novo. This is:
- More computationally efficient
- Better suited for well-annotated genomes
- The standard approach for differential expression studies

---

## 3. Dataset

### Samples

| Sample | Condition | Sequencing Type | GEO Accession |
|--------|-----------|-----------------|---------------|
| GSM461176 | Untreated | Single-end | [GSM461176](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM461176) |
| GSM461177 | Untreated | Paired-end | [GSM461177](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM461177) |
| GSM461178 | Untreated | Paired-end | [GSM461178](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM461178) |
| GSM461179 | Treated (ps KD) | Single-end | [GSM461179](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM461179) |
| GSM461180 | Treated (ps KD) | Paired-end | [GSM461180](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM461180) |
| GSM461181 | Treated (ps KD) | Paired-end | [GSM461181](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM461181) |
| GSM461182 | Untreated | Single-end | [GSM461182](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM461182) |

> ⚠️ **Note:** For tutorial purposes, all FASTQ files were subsampled to a fraction of their original size to reduce computation time on Galaxy Europe.

### Reference Files

| File | Description |
|------|-------------|
| Reference Genome | *Drosophila melanogaster* **dm6** |
| Gene Annotation | Ensembl **BDGP6.87** (GTF format) |
| Data Source | [Zenodo Record 6457007](https://zenodo.org/record/6457007) |

---

## 4. Pipeline Overview

```
Raw FASTQ Reads (7 samples: 3 treated, 4 untreated)
         │
         ▼
 ┌─────────────────────┐
 │   Quality Control    │  Falco + MultiQC
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │    Read Trimming     │  Cutadapt
 │                      │  • Remove Illumina adapters
 │                      │  • Trim low-quality 3' bases (Q < 20)
 │                      │  • Discard reads < 20 bp
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │  Spliced Alignment   │  RNA STAR → dm6 genome + BDGP6.87 GTF
 │  + Visualization     │  IGV + JBrowse2
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │  Strand Coverage     │  STAR wiggle output
 │                      │  Strand 1 (blue) + Strand 2 (red)
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │   Gene-Level         │  featureCounts
 │   Read Counting      │  • Count reads per gene
 │                      │  • Feature type: exon
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │  Differential        │  DESeq2
 │  Expression          │  • Negative binomial model
 │  Analysis            │  • Design: ~ type + condition
 │                      │  • PCA Plot
 │                      │  • Sample Distance Heatmap
 │                      │  • MA Plot
 └──────────┬──────────┘
            │
            ▼
   Differentially Expressed Genes (DEGs)
   padj < 0.05 | |log2FC| > 1
```

---

## 5. Section 1 — Quality Control

📂 [View Section 1 Files](./01_quality_control/)

### Objective
Assess the quality of raw sequencing reads before any processing, to identify issues such as low-quality bases, adapter contamination, or GC content bias.

### Tools
- **Falco** — a fast, drop-in replacement for FastQC; generates per-sample HTML quality reports
- **MultiQC** — aggregates all Falco reports into a single interactive HTML summary

### Key Metrics Evaluated

| Metric | What It Checks |
|--------|---------------|
| Per-base sequence quality | Phred scores along read length (aim: Q ≥ 28) |
| Per-sequence quality scores | Distribution of mean quality per read |
| Sequence length distribution | Consistency of read lengths |
| GC content | Expected ~50%; deviations may indicate contamination |
| Adapter content | Presence of Illumina adapter sequences |
| Sequence duplication | High duplication can indicate PCR over-amplification |

### Outputs
- `multiqc_raw_reads_report.html` — aggregate QC before trimming
- `multiqc_after_trimming_report.html` — aggregate QC after trimming
- `falco_webpage.html` — individual Falco report

---

## 6. Section 2 — Read Trimming

### Objective
Remove adapter sequences and low-quality bases to improve downstream alignment accuracy.

### Tool: Cutadapt

Key parameters applied:
- Adapter sequence: Illumina TruSeq universal adapter
- Quality cutoff: **Q20** (3' end trimming)
- Minimum read length after trimming: **20 bp**

### Expected Outcomes
- Elimination of adapter-contaminated read portions
- Improved per-base quality scores, especially at 3' ends
- Minimal read loss (typically < 5% of total reads discarded)

---

## 7. Section 3 — Alignment & Visualization

📂 [View Section 2 Files](./02_alignment/)

### Objective
Map trimmed reads to the *D. melanogaster* reference genome (dm6), correctly handling reads that span exon-exon splice junctions.

### Tool: RNA STAR (v2.7.11b)

RNA STAR performs **splice-aware alignment**, meaning it can correctly map reads that originate from spliced mRNA transcripts — essential for RNA-Seq data.

**Key parameters:**

| Parameter | Value |
|-----------|-------|
| Read type | Single-end and paired-end (as collections) |
| Reference genome | dm6 (built-in Galaxy index) |
| Gene model (GTF) | `Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz` |
| Junction overhang length | 36 bp (read length − 1) |
| Per-gene output | GeneCounts mode |

### Visualization
Aligned BAM files were visualized using:
- **IGV** — desktop genome browser for inspecting individual reads and splice junctions
- **JBrowse2** — web-based genome browser embedded within Galaxy for sharing and screenshots

Visualization confirms that reads are mapping to expected exonic regions across the genome, with visible coverage differences between conditions at the Pasilla locus.

### Outputs
- `jbrowse2_alignments_chr4.png` — screenshot of read alignments on chromosome 4
- `multiqc_star_alignment_report.html` — per-sample alignment statistics summary

---

## 8. Section 4 — STAR Strand Coverage

📂 [View Section 3 Files](./03_star_coverage/)

### Objective
Visualize strand-specific read coverage to confirm library strandedness and examine directional gene expression.

### Tool: STAR (Wiggle/Coverage Output)

RNA STAR generates strand-specific coverage files (wiggle format) alongside BAM output. These are loaded into JBrowse2 as two separate tracks:

| Track | Color | Strand |
|-------|-------|--------|
| Strand 1 | 🔵 Blue | Forward (+) strand |
| Strand 2 | 🔴 Red | Reverse (−) strand |

### Biological Interpretation
Strand-specific plots confirm:
- Library strandedness (important for correct featureCounts settings)
- Absence of antisense contamination
- Directional expression differences between treated and untreated samples

### Output
- `jbrowse_strand1_blue_strand2_red.png` — strand-specific coverage visualization in JBrowse2

---

## 9. Section 5 — Gene Counting

📂 [View Section 4 Files](./04_gene_counting/)

### Objective
Produce a count matrix by quantifying how many reads overlap each annotated gene, which serves as the primary input for differential expression analysis.

### Tool: featureCounts

featureCounts assigns aligned reads to features defined in the GTF annotation file.

**Key parameters:**

| Parameter | Value |
|-----------|-------|
| Feature type | `exon` |
| Gene attribute | `gene_id` |
| Strandedness | Based on RSeQC Infer Experiment results |
| Multi-mapping reads | Excluded (unique reads only) |

### Output
- `featurecounts_summary.tabular` — per-sample count matrix plus assignment statistics (assigned, unassigned ambiguous, unassigned no features, etc.)

---

## 10. Section 6 — Differential Expression Analysis

📂 [View Section 5 Files](./05_differential_expression/)

### Objective
Statistically identify genes that are significantly differentially expressed between the Pasilla knockdown (treated) and control (untreated) conditions.

### Tool: DESeq2

DESeq2 applies a **negative binomial model** to RNA-Seq count data. It uses **shrinkage estimation** of log₂ fold changes, which improves accuracy for genes with low read counts. Multiple testing correction is performed using the **Benjamini-Hochberg** method to control the False Discovery Rate (FDR).

### Experimental Design

```
design = ~ type + condition
```

| Variable | Role | Values |
|----------|------|--------|
| `type` | Batch/technical factor | single-end, paired-end |
| `condition` | Variable of interest | treated, untreated |

Including `type` in the design accounts for technical variation between single-end and paired-end samples.

### Significance Thresholds

| Criterion | Threshold |
|-----------|-----------|
| Adjusted p-value (FDR) | < 0.05 |
| Log₂ Fold Change | > 1 or < −1 |

### Output Files

| File | Description |
|------|-------------|
| `deseq2_results.tabular` | Full results: gene ID, baseMean, log₂FC, lfcSE, stat, p-value, padj |
| `deseq2_normalized_counts.tabular` | Size-factor normalized expression counts per sample |
| `deseq2_plots.pdf` | PCA plot, sample-to-sample heatmap, MA plot |

### Visualizations

| Plot | Purpose |
|------|---------|
| **PCA Plot** | Confirms treated and untreated samples cluster separately (PC1 separates conditions) |
| **Sample Distance Heatmap** | Hierarchical clustering of all 7 samples based on expression profiles |
| **MA Plot** | Log₂ fold change vs. mean normalized expression; significant DEGs highlighted in red |

---

## 11. Key Results Summary

| Analysis Stage | Outcome |
|----------------|---------|
| Quality Control | All 7 samples passed QC thresholds; minor adapter content detected and removed |
| Read Trimming | > 95% reads retained after trimming |
| Alignment | > 80% unique mapping rate across all samples to dm6 |
| Gene Counting | Majority of reads assigned to annotated exonic features |
| Differential Expression | Pasilla gene significantly downregulated in treated samples (padj < 0.05); results consistent with known RNAi knockdown |

> 📊 Full result tables and plots are available in the `05_differential_expression/outputs/` directory.

---

## 12. Tools & Software

| Tool | Version | Purpose | Reference |
|------|---------|---------|-----------|
| Galaxy Europe | — | Cloud bioinformatics platform | Afgan et al., 2022 |
| Falco | Latest | Per-sample read QC | — |
| MultiQC | 1.27 | Aggregate QC reports | Ewels et al., 2016 |
| Cutadapt | Latest | Adapter trimming | — |
| RNA STAR | 2.7.11b | Splice-aware alignment | Dobin et al., 2013 |
| IGV | Latest | Desktop genome browser | — |
| JBrowse2 | Latest | Web genome browser | — |
| featureCounts | Latest | Gene-level read counting | Liao et al., 2014 |
| DESeq2 | Latest | Differential expression | Love et al., 2014 |

---

## 13. How to Reproduce

1. **Create a Galaxy account** at [usegalaxy.eu](https://usegalaxy.eu/)
2. **Import the dataset** from [Zenodo record 6457007](https://zenodo.org/record/6457007) using Galaxy's URL upload or shared data library
3. **Follow the pipeline** step-by-step in order (Sections 1 → 5), using parameters documented in each section's README
4. **Alternatively**, import the Galaxy Training Network workflow directly for automated pipeline execution
5. All tool versions, parameters, and expected outputs are fully documented in the section-level README files

> ✅ All analyses use publicly available data and open-source tools. The pipeline is fully reproducible on any Galaxy server with the required tools installed.

---

## 14. References

1. Batut, B., et al. (2018). Reference-based RNA-Seq data analysis. *Galaxy Training Network.* https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html

2. Dobin, A., et al. (2013). STAR: ultrafast universal RNA-seq aligner. *Bioinformatics*, 29(1), 15–21. https://doi.org/10.1093/bioinformatics/bts635

3. Liao, Y., Smyth, G. K., & Shi, W. (2014). featureCounts: an efficient general purpose program for assigning sequence reads to genomic features. *Bioinformatics*, 30(7), 923–930. https://doi.org/10.1093/bioinformatics/btt656

4. Love, M. I., Huber, W., & Anders, S. (2014). Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. *Genome Biology*, 15, 550. https://doi.org/10.1186/s13059-014-0550-8

5. Ewels, P., et al. (2016). MultiQC: summarize analysis results for multiple tools and samples in a single report. *Bioinformatics*, 32(19), 3047–3048. https://doi.org/10.1093/bioinformatics/btw354

6. Brooks, A. N., et al. (2011). Conservation of an RNA regulatory map between Drosophila and mammals. *Genome Research*, 21(2), 193–202. https://doi.org/10.1101/gr.108662.110

7. Afgan, E., et al. (2022). The Galaxy platform for accessible, reproducible, and collaborative data analyses. *Nucleic Acids Research*, 50(W1), W345–W351. https://doi.org/10.1093/nar/gkac247

---

> 📌 *This repository was prepared as part of **BI-436: Special Topics in Bioinformatics** at the National University of Sciences and Technology (NUST), Spring 2026. All analyses were performed on Galaxy Europe using the publicly available Pasilla dataset (GEO accession: GSE18508).*
