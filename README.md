# Phylogenetics In-Class Activity for DGP-485
## DGP-485 Data Science for Biomedical Researchers

## April 15, 2024

_Ramon Lorenzo-Redondo, Ph.D._ (ramon.lorenzo@northwestern.edu)

### 1. Download the required software to your computer
**Seaview**:https://doua.prabi.fr/software/seaview

**FigTree**: https://github.com/rambaut/figtree/releases

**Unipro UGENE**:http://ugene.net/

### 2. Set up environment on Quest
Although the exercises can be run on a personal computer, we will set up a conda environment to get familiar with command line and setting up environments with all necessary tools. Use the following command to install the rest of the tools in your new environment. You can also use your own computer if mamba or conda is installed.

**Load Mamba module (not necessary if using own device):**
```
module load mamba
```


**Create environment with software:**
```
mamba create -c bioconda -n Phylogenetics_DGP485 \
mafft \
iqtree \
treetime
```

**Activate environment:**
```
mamba activate Phylogenetics_DGP485
```

**Create a working folder in your home directory for results.**

```
mkdir ~/Phylogenetics_DGP485

cd ~/Phylogenetics_DGP485

```

**Copy all materials.**
```
cp -r /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/* .
```


### Phylogenetic analyses
#### 1. Alignments
After understanding the basic concepts of Multiple Sequence Alignment, we are going to practice with three different sets of sequences. This way we will be able to appreciate the meaining of the concepts we have presented.
We will generate an alignment with a low variability set, using SARS-CoV-2 full genome sequences obtained from **GISAID**(https://gisaid.org/). We will generate two other alignments with a highly variable virus, HIV-1 with sequences dowloades from the HIV sequence database (https://www.hiv.lanl.gov/content/index). We will analyze the difference between the Polymerase  and the Envelope (receptor binding protein) genes.

**Low variability alignment**
```
mafft --auto --thread -1 SARSCoV2_BA4.fasta > SARSCoV2_BA4.sequences_aligned.fasta
```
**Medium variability alignment**
```
mafft --auto --thread -1 HIV1_CON_2021_pol_DNA.fasta > HIV1_CON_2021_pol_DNA_aligned.fasta
```
**High variability alignment**
```
mafft --auto --thread -1 HIV1_CON_2021_env_DNA.fasta > HIV1_CON_2021_env_DNA_aligned.fasta
```

Visualize each alignment with **seaview**.

#### 2. Tree building (ML Phylogenies)

Now that we have covered the theoretical background behind phylogenetic tree inference, we are going to practice using the ML method. We will analyze the intra-host evolution of HIV-1 using data from a study performed by Liao et a. (_Co-evolution of a broadly neutralizing HIV-1 antibody and founder virus. Nature 497(7446):469-76, 2013. doi:10.1038/nature12053 PMID 23552890._) 

```
iqtree2 -s IntraHostHIVenvLiao2013.fasta -T AUTO -B 1000 
```

We will visualize the tree with **FigTree**

#### 3. Selection Analysis

To get exposed to selection analysis we will do a short exercise using the Datamonkey server. We will use a version of the previous alignment that was modified to be in-frame.

  1. Go to: http://www.datamonkey.org/

  2. Select Episodic Site Selction Analysis (MEME)

  3. Upload File: IntraHostHIVenvLiao2013_NoStop_subalign.fasta

  4. Check results and identify sites under positive selection. 

We will view the alignment using the **Highlighter** tool from the HIV sequence database from Los Alamos.

  1. Go to: https://www.hiv.lanl.gov/content/sequence/HIGHLIGHT/highlighter_top.html

  2. Upload File: IntraHostHIVenvLiao2013_NoStop_subalign.fasta

#### 4. Phylodynamics

We will apply simple concepts of coalescence and phylodynamics to analyze the origin of the BA.4 lineage of SARS-CoV-2. We will use the alignment generated in **Exercise 1**. We will use TreeTime to perform this analysis.

  1. Check the alignment and assess QC with **Ugene**

  2. Generate an ML tree as in **Exercise 2**
```
iqtree2 -s SARSCoV2_BA4.sequences_aligned.fasta -T AUTO -B 1000
```
  3. Use the output and metadata including dates to generate a temporal tree and coalescence analysis using ML approach.
  
```
treetime --confidence --relax 1.0 0.5 --aln SARSCoV2_BA4.sequences_aligned.fasta --tree SARSCoV2_BA4.sequences_aligned.fasta.treefile --dates SARSCoV2_BA4.metadata.tsv --coalescent skyline --clock-filter 4 --clock-rate 0.0008 --clock-std-dev 0.0004 --branch-length-mode marginal --outdir timetree_out
```
  4. Perform ML ancestral state reconstruction to analyze phylogeography.

```
treetime mugration --tree timetree_out/timetree.nexus --states SARSCoV2_BA4.metadata.tsv --attribute country
```


