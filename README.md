# Genome Assembly Pipeline

This repository provides a detailed step-by-step guide for genome assembly using **Rhodobacter sphaeroides** dataset as the primary example. It covers key processes such as data preprocessing, genome assembly, evaluation, visualization, comparison, and annotation. Additionally, it includes a guide for genome assembly using a downsampled **Zaire Ebolavirus** dataset for rapid analysis.

---

## Table of Contents

1. [Server Information](#1-server-information)  
2. [Dataset](#2-dataset)  
3. [Quality Visualization](#3-quality-visualization)  
4. [Quality Filtering and Trimming](#4-quality-filtering-and-trimming)  
5. [Genome Assembly](#5-genome-assembly)  
6. [Assembly Evaluation](#6-assembly-evaluation)  
7. [Assembly Visualization](#7-assembly-visualization)  
8. [Comparing Genomes](#8-comparing-genomes)  
9. [Genome Annotation](#9-genome-annotation)  
10. [Exercise](#10-exercise)
    - [Dataset](#exercise-dataset)  
    - [Quality Visualization](#exercise-dataset-quality-visualization)  
    - [Quality Filtering and Trimming](#exercise-dataset-quality-filtering-and-trimming)  
    - [Genome Assembly](#exercise-dataset-genome-assembly)  
    - [Assembly Evaluation](#exercise-dataset-assembly-evaluation)  
    - [Assembly Visualization](#exercise-dataset-assembly-visualization)  
    - [Comparing Genomes](#exercise-dataset-comparing-genome)  
    - [Genome Annotation](#exercise-dataset-genome-annotation)  
12. [Educational Notes](#11-educational-notes)
---

## 1. Server Information

### Prerequisites
- **Tools:** Install  [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), [FileZilla](https://filezilla-project.org/download.php), [Bandage](http://rrwick.github.io/Bandage/), [Mauve](https://darlinglab.org/mauve/mauve.html).  
- **Server Details:**  
  - **IP Address:** `168.105.161.70`  
  - **Port:** `22`  
  - **Access:** Requires JABSOM or UH network (use VPN for remote access).
  - Note: With PuTTY and FileZilla you can connect to server.

### Security Practices
- Avoid multiple failed login attempts to prevent account locking.  
- Use strong passwords or SSH keys.  
- Log out after completing tasks.

### Installing Miniconda
```bash
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
source ~/.bashrc
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

---

## 2. Dataset

### Dataset Information
- **Organism:** Rhodobacter sphaeroides  
- **SRA ID:** [SRR522246](https://www.ncbi.nlm.nih.gov/sra/?term=SRR522246)  
- **Size:** Paired-end reads (~4.6 GB each, total 9.2 GB).  

### Why This Dataset?
This dataset was chosen for its small genome size, high-quality sequencing data, and suitability for teaching genome assembly principles.

### Download Dataset
1. Create a directory for the assembly process:
    ```bash
    mkdir -p assembly/rhodobacter
    cd assembly/rhodobacter
    ```
2. Use `fastq-dump` to download paired-end reads:
    ```bash
    fastq-dump --split-files SRR522246
    ```





---

## 3. Quality Visualization

### Why Perform Quality Control?
- Identify low-quality bases, adapter contamination, and sequencing artifacts.
- Ensure reliable downstream analysis.

### Steps
1. Create a directory for quality control results:
    ```bash
    mkdir qc
    ```
2. Run FastQC to assess the quality of raw reads:
    ```bash
    fastqc *.fastq -o qc/
    ```
3. Aggregate and summarize results using MultiQC:
    ```bash
    multiqc qc/
    ```

### Key Metrics to Examine
- **Per-base sequence quality**: Should be high across all cycles.  
- **GC content**: Compare with the expected range for the organism.  
- **Overrepresented sequences**: Indicates adapter contamination.

---

## 4. Quality Filtering and Trimming

### Why Trim Reads?
- Improves downstream processes by removing low-quality regions and adapter sequences.

### Steps
1. Download adapter sequences:
    ```bash
    curl -OL https://raw.githubusercontent.com/BioInfoTools/BBMap/master/resources/adapters.fa
    ```
2. Trim reads using Trimmomatic:
    ```bash
    trimmomatic PE SRR522246_1.fastq SRR522246_2.fastq \
      trimmed_1.fastq unpaired_1.fastq \
      trimmed_2.fastq unpaired_2.fastq \
      ILLUMINACLIP:adapters.fa:2:30:10 \
      LEADING:20 TRAILING:20 AVGQUAL:20 MINLEN:20
    ```

### Explanation of Parameters
- **ILLUMINACLIP**: Removes adapter sequences.  
- **LEADING/TRAILING**: Trims low-quality bases at read ends.  
- **AVGQUAL**: Filters reads with average quality below the threshold.  
- **MINLEN**: Discards short reads.

---

## 5. Genome Assembly

### What is Genome Assembly?
Reconstructing a genome by piecing together sequencing reads.

### Steps
1. Combine unpaired reads:
    ```bash
    cat unpaired_1.fastq unpaired_2.fastq > unpaired.fastq
    ```
2. Assemble the genome using SPAdes:
    ```bash
    spades.py -k 21,33,55,77,99 --careful -o spades_output \
      -1 trimmed_1.fastq -2 trimmed_2.fastq -s unpaired.fastq
    ```

### Notes
- SPAdes uses multiple k-mers for robust assembly.  
- The `--careful` flag reduces errors like mismatches and indels.

---

## 6. Assembly Evaluation

### Why Evaluate?
- Assess the quality, contiguity, and completeness of the assembly.

### Steps
1. Download the reference genome:
    ```bash
    esearch -db nucleotide -query NC_007493 | efetch -format fasta > reference.fasta
    ```
2. Evaluate the assembly using QUAST:
    ```bash
    quast -R reference.fasta spades_output/scaffolds.fasta
    ```

### Key Metrics
- **N50**: Larger values indicate better assembly contiguity.  
- **Misassemblies**: Highlight potential errors in scaffolding.  

---

## 7. Assembly Visualization

### Why Visualize?
- Understand the structure of the assembly and detect misassemblies.

### Steps
1. Install [Bandage](http://rrwick.github.io/Bandage/).
2. Load the SPAdes assembly graph:
    ```bash
    spades_output/assembly_graph_with_scaffolds.gfa
    ```
3. Explore the graph to identify linear and branched regions.

---

## 8. Comparing Genomes

### Why Compare?
Identify structural variations, conserved regions, and evolutionary differences.

### Steps
1. Install [Mauve](http://darlinglab.org/mauve/mauve.html).
2. Align the assembled genome against a reference:
    - Input: Assembled scaffolds (`spades_output/scaffolds.fasta`) and reference genome.

---

## 9. Genome Annotation

### Why Annotate?
- Predict genes, their functions, and associated pathways.

### Steps
1. Use [RAST](https://rast.nmpdr.org/rast.cgi) for prokaryotic genome annotation:
    - Upload `spades_output/scaffolds.fasta`.  
    - Select genetic code table 11 (bacterial genomes).  
2. Review the annotated genes and predicted functions.

### Alternatives
- **Prokka**: Command-line tool for rapid genome annotation.  
- **ORFfinder**: Predicts open reading frames (ORFs).

---

## 10. Exercise

### Exercise Dataset: Zaire Ebolavirus
- **Dataset Information
- **Source**: NCBI Sequence Read Archive (SRA)
- **SRA ID:** [SRR1553425](https://www.ncbi.nlm.nih.gov/sra/SRR1553425)
- **Details**: Paired-end sequencing data from the 2014 Zaire ebolavirus outbreak in Sierra Leone. 
- **Subset:** Use only 100,000 paired-end reads for faster processing:
    ```bash
    mkdir -p assembly/ebola
    cd assembly/ebola
    fastq-dump --split-files -X 100000 SRR1553425
    ```

### Exercise Dataset: Quality Visualization 
```bash
mkdir qc
fastqc *.fastq -o qc/
multiqc .
```
- Transfer qc and multiqc results to your local machine and view HTML report

### Exercise Dataset: Quality Filtering and Trimming
```bash
curl –OL https://raw.githubusercontent.com/BioInfoTools/BBMap/master/resources/adapters.fa > adapters.fa
trimmomatic PE SRR1553425_1.fastq SRR1553425_2.fastq trimmed_1.fastq unpaired_1.fastq trimmed_2.fastq unpaired_2.fastq ILLUMINACLIP:adapters.fa:2:30:10 LEADING:20 TRAILING:20 AVGQUAL:20 MINLEN:20
``` 

### Exercise Dataset: Genome Assembly
```bash
cat unpaired_1.fastq unpaired_2.fastq > unpaired.fastq
spades.py -k 21,33,55,77,99 --careful -o spades_output -1 trimmed_1.fastq -2 trimmed_2.fastq -s unpaired.fastq
```

### Exercise Dataset: Assembly Evaluation 
```bash
conda install -c bioconda entrez-direct
or 
mamba install entrez-direct
[NC_002549](https://www.ncbi.nlm.nih.gov/nuccore/10313991)
 esearch -db nucleotide -query NC_002549 | efetch -format fasta > ref_genome.fa
 quast -R ref_genome.fa spades_output/scaffolds.fasta
 ```
- Copy the results folder locally via FileZilla and check out the HTML report


### Exercise Dataset: Assembly Visualization 
- Download [Bandage](https://rrwick.github.io/Bandage/). We will use Bandage to visualize the assembly graph. Download and install this on your computer. 
- When it is open, hit File->Load graph in the main menu. 
- Here pick your final SPAdes assembly graph that you downloaded, spades_output/assembly_graph.fastg. 
- Then hit the “Draw graph” button on the left side. You’ll see we got clean assembly results.
- To see a messier assembly result, you can load the graph spades_output/K21/assembly_graph.fastg. 
- This is the assembly graph using 21-mers and it didn’t turn out as well.

### Exercise Dataset: Comparing Genome
- Install [Mauve](https://darlinglab.org/mauve/mauve.html)
- First download Ebola virus reference genomes (the .gb files) to compare
- https://www.ncbi.nlm.nih.gov/nuccore/NC_002549.1?report=genbank&log$=seqview
- Open Mauve. Then on the main menu select File->Align with progressiveMauve. 
- This will trigger a pop-up to specify your input genomes. 
- Click “Add Sequence” and select all the reference genomes you downloaded (the .gb files) and click “Open”. 
- Click “Add Sequence” again but this time select your spades_output/scaffolds.fasta file and hit “Open”. 
- Finally, click “Align” to process your data.
- This will produce a visualization comparing the conservation of various regions across these assembled genomes. 
- Since we are using GenBank files (.gb) for our references, we will also get gene annotations as part of the visualization.

### Exercise Dataset: Genome Annotation 
- Lastly, let’s annotate possible genes our assembled genome. 
- First we’ll find open reading frames (ORFs) using NCBI’s ORFfinder. https://www.ncbi.nlm.nih.gov/orffinder/
- Copy and paste only the first contig from your spades_output/scaffolds.fasta file. 
- Submit with the default settings.
- To download all ORF results into a FASTA file, on the bottom right box, press “Mark subset…” and pick “All ORFs.” 
- Then press “Download marked set.” By default, this will save the protein predictions. 
- You can also download CDS predictions by clicking on the drop down and changing “Protein FASTA” to “CDS FASTA”.
- With the list of ORFs, you can now use BLAST to try to predict the function of the ORF. 
- Use Nucleotide BLAST for the CDS regions and Protein BLAST for the protein file.

---


## 11. Educational Notes

### Key Takeaways
- **Base Quality (FastQC):** High-quality data improves assembly.  
- **Trimming Parameters:** Balance between removing contaminants and retaining data.  
- **Assembly Metrics:** High N50 and low misassembly rates indicate good assembly.  
- **Annotation Tools:** Experiment with different tools to cross-validate results.

---




## Contributing

Contributions to improve this pipeline are welcome!
---
