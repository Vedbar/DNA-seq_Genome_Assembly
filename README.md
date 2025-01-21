# Genome Assembly Pipeline

This repository provides a detailed step-by-step guide for genome assembly using **Rhodobacter sphaeroides** and **Zaire Ebolavirus** datasets. It includes data preprocessing, assembly, evaluation, visualization, comparison, and annotation.

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

---

## 1. Server Information

### Prerequisites
- **Tools:** Install PuTTY, FileZilla, Bandage, Mauve.  
- **Server Details:**  
  - **IP Address:** `168.105.161.70`  
  - **Port:** `22`  
  - **Access:** Requires JABSOM or UH network (use VPN for remote access).  

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

