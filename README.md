# DNA-seq_Genome_Assembly

This repository contains the materials, scripts, and results from the Genome Assembly Lecture. The lecture is designed to introduce participants to the tools and techniques used for genome assembly, using the Zaire ebolavirus dataset as a case study.

## Overview

This lecture covers the following key topics:
- **Data Preparation**: Downloading and processing sequencing data.
- **Quality Control**: Visualizing and trimming raw sequencing reads.
- **Genome Assembly**: Constructing the genome using SPAdes.
- **Evaluation and Annotation**: Assessing assembly quality and annotating genes.

## Dataset Information
- **Source**: NCBI Sequence Read Archive (SRA)
- **Accession ID**: [SRR1553425](https://www.ncbi.nlm.nih.gov/sra/SRR1553425)
- **Details**: Paired-end sequencing data from the 2014 Zaire ebolavirus outbreak in Sierra Leone.

## Tools and Software
- **FastQC**: Quality control of raw reads.
- **Trimmomatic**: Quality filtering and adapter trimming.
- **SPAdes**: Genome assembly.
- **QUAST**: Assembly quality evaluation.
- **Bandage**: Assembly visualization.
- **Mauve**: Genome comparison.
- **NCBI ORFfinder**: Gene prediction.
- **BLAST**: Functional annotation.

## Steps in the Analysis
1. **Data Downloading**:
   - Used `fastq-dump` to retrieve a 100,000-read subset of the dataset.
2. **Quality Control**:
   - Analyzed raw reads using `FastQC`.
3. **Quality Filtering**:
   - Trimmed low-quality reads and adapters using `Trimmomatic`.
4. **Genome Assembly**:
   - Constructed the genome with SPAdes.
5. **Assembly Evaluation**:
   - Assessed quality using `QUAST`.
6. **Visualization and Annotation**:
   - Visualized assembly graphs with Bandage.
   - Predicted genes and annotated functions with ORFfinder and BLAST.

## Key Findings
- High-quality assembly with minimal mismatches and indels.
- Successful identification of key open reading frames (ORFs) related to viral genes.

## Folder Structure
- `scripts/`: Scripts used in the analysis.
- `results/`: Generated files, including assemblies and quality reports.
- `data/`: Input datasets.
- `visualizations/`: Graphs and comparative analyses.

## Contact
For any questions or suggestions, feel free to contact:
- **Dr. Vedbar Khadka**
- Bioinformatics and Biostatistics Core, JABSOM, University of Hawaii
- Email: [your_email@example.com]
