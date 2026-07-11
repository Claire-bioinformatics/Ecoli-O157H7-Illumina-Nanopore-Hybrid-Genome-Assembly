# De Novo Genome Assembly of *E. coli* O157:H7: Illumina-Only vs. Illumina+Nanopore Hybrid

Comparing a short-read-only assembly against a short+long-read hybrid assembly of the *E. coli* O157:H7 genome, to test whether adding Oxford Nanopore long reads improves assembly completeness and/or accuracy.

## Project brief

Using paired-end Illumina MiSeq short reads and Oxford Nanopore MinION long reads from *E. coli* O157:H7, the task was to:

1. Assess raw Illumina read quality (FastQC)
2. Trim adapters/low-quality bases (Trim Galore) and re-assess quality
3. Assemble the trimmed Illumina reads alone (SPAdes) and evaluate against the reference genome (QUAST)
4. Assemble the trimmed Illumina reads **together with** the Nanopore long reads (SPAdes) and evaluate again (QUAST)
5. Compare the two assemblies' key metrics and judge which is more suitable for downstream use

All steps were run on a Galaxy server (point-and-click NGS workflow platform — no local scripting was involved). The original task brief is included at [`docs/task_brief.pdf`](docs/task_brief.pdf).

## Repository contents

```
report/
  report.pdf   # Full mini scientific paper: summary, intro, methods, results, discussion, conclusion, references
docs/
  task_brief.pdf   # Original task instructions
```

## Pipeline

| Step | Tool | Purpose |
|---|---|---|
| Raw read QC | FastQC v0.12.1 | Assess per-base quality, GC content, adapter content of raw Illumina reads |
| Trimming | Trim Galore v0.6.7 | Remove adapters; trim bases below Phred 20; discard reads <100 bp post-trim |
| Trimmed read QC | FastQC v0.12.1 | Confirm quality improvement post-trimming |
| Assembly 1 (short-read only) | SPAdes v3.15.4 | *De novo* assembly from trimmed Illumina reads only (k-mers 21–127) |
| Assembly 2 (hybrid) | SPAdes v3.15.4 | *De novo* assembly from trimmed Illumina reads + Nanopore long reads |
| Assembly evaluation | QUAST | Compare both assemblies against the *E. coli* O157:H7 EDL933 reference genome (contig length threshold 1000 bp) |

## Key results

- **Read quality:** Trim Galore removed adapter-containing/low-quality bases (~24% of reads carried adapters; 3.92% of read pairs dropped below the 100 bp cutoff), producing uniformly high per-base quality (all positions median Phred >20) and stable per-base sequence content post-trimming.
- **Assembly comparison (QUAST):**

  | Metric | Illumina-only | Hybrid (Illumina + Nanopore) |
  |---|---|---|
  | Genome fraction covered | 95.10% | 97.00% |
  | N50 / NG50 | 148,373 / 146,855 bp | 3,572,033 bp |
  | Contigs (≥1kb) | 95 | 12 |
  | Misassembled contigs | 1 | 3 |
  | Misassembled length | 98,856 bp | 4,658,887 bp (~86% of assembly) |

- **Conclusion:** the hybrid assembly achieved much higher contiguity (far fewer, much longer contigs) and slightly better genome coverage, but at the cost of a dramatically higher misassembly rate (~86% of its total contig length). The Illumina-only assembly, while more fragmented, was structurally far more reliable. For downstream analyses where accuracy matters more than contiguity, the **Illumina-only assembly** was judged preferable — see [`report/report.pdf`](report/report.pdf) for the full discussion, including how coverage, hybrid-specific assemblers (e.g. MaSuRCA), and PacBio HiFi reads + DEGAP could push future assemblies toward a single accurate contig.

## Tools

Galaxy, FastQC, Trim Galore, SPAdes, QUAST.
