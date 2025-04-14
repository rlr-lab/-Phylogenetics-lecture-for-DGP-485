# Phylogenetics In-Class Activity for DGP-485
## DGP-485 Data Science for Biomedical Researchers

## April 14, 2025

_Ramon Lorenzo-Redondo, Ph.D._ (ramon.lorenzo@northwestern.edu)

### 1. Download the required software to your computer
### Step 1.A - FigTree  

[FigTree](http://tree.bio.ed.ac.uk/software/figtree/) is a program to view phylogenetic trees. Files that can be viewed in FigTree generally end with the suffix `.tre` or `.nwk`.  

* To install, download the most recent version (v1.4.4) from this website: [https://github.com/rambaut/figtree/releases](https://github.com/rambaut/figtree/releases). 
* If you have a Mac, download the file ending with `.dmg`. Create a new folder in your Applications folder and drag all of the files into that folder.
* If you have a PC, download the file ending with `.zip`. Double-click and move all of the files into a new folder
* If you have Linux, download the file ending with `.tgz`. Double-click and move all of the files into a new folder.

### Step 1.B - SeaView  

[SeaView](http://doua.prabi.fr/software/seaview) is a program that can view and generate multiple sequence alignments of DNA and protein sequence as well as multiple other functions.

* To install, download the correct version for your computer from here: [http://doua.prabi.fr/software/seaview](http://doua.prabi.fr/software/seaview) and follow the instructions on that site for proper installation. 
* On Macs, you'll probably get a security alert when you try to open the program for the first time. To override this (it's OK!) open your _System Preferences_, click the _Security & Privacy_ icon, then click "Allow" next to SeaView in the window. Should be good to go.


### 2. Set up environment on Quest
Although the exercises can be run on a personal computer, we will set up a conda environment to get familiar with command line and setting up environments with all necessary tools. Use the following command to install the rest of the tools in your new environment. 

**Log into Quest**

```
ssh <NETID>@login.quest.northwestern.edu
```
Where <NETID> is replaced with your NetID (such as abc123 for example).

Then enter your NetID password and hit Enter.


**Move to the lecture folder**

```
cd /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/
```

**Create a working folder for results.**

```
mkdir <NETID>

cd <NETID>

```
Again, here <NETID> is replaced with your NetID 


**Load Mamba module:**
```
module load mamba
```

**Activate environment with software:**
I previously created an environment with necessary software

```
conda activate /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/conda_envs/Phylogenetics_DGP485
```
*If you have never used mamba on Quest you will have to do this:*

```
conda init bash
source ~/.bashrc
```
*If you want to create the environment later somewhere else:*
```
mamba create -c bioconda -n Phylogenetics_DGP485 \
mafft \
iqtree \
treetime \
hyphy
```

### Phylogenetic analyses
#### 1. Alignments
After understanding the basic concepts of Multiple Sequence Alignment, we are going to practice with three different sets of sequences. This way we will be able to appreciate the meaining of the concepts we have presented.
We will generate an alignment with a low variability set, using SARS-CoV-2 full genome sequences obtained from **GISAID**(https://gisaid.org/). We will generate two other alignments with a highly variable virus, HIV-1 with sequences dowloades from the HIV sequence database (https://www.hiv.lanl.gov/content/index). We will analyze the difference between the Polymerase  and the Envelope (receptor binding protein) genes.

**Low variability alignment**
We will use the a reference genome to speed up the process.
```
mafft --auto --thread -1 --keeplength --addfragments /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/SARSCoV2_BA4.fasta /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/CoV2RefSeq.fasta > SARSCoV2_BA4.sequences_aligned.fasta
```
**Medium variability alignment**
```
mafft --auto --thread -1 /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/HIV1_CON_2021_pol_DNA.fasta > HIV1_CON_2021_pol_DNA_aligned.fasta
```
**High variability alignment**
```
mafft --auto --thread -1 /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/HIV1_CON_2021_env_DNA.fasta > HIV1_CON_2021_env_DNA_aligned.fasta
```

Visualize each alignment with **seaview**. For this download the files to your computer using either a **remote file broswer** or command line such as:

```
scp -r <NETID>@quest.northwestern.edu:/projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/<FILE> <PATH-to-folder>
```
Then enter your NetID password and hit Enter.


#### 2. Tree building (ML Phylogenies)

Now that we have covered the theoretical background behind phylogenetic tree inference, we are going to practice using the ML method. We will analyze the intra-host evolution of HIV-1 using data from a study performed by Liao et a. (_Co-evolution of a broadly neutralizing HIV-1 antibody and founder virus. Nature 497(7446):469-76, 2013. doi:10.1038/nature12053 PMID 23552890._) 

```
iqtree2 -s /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/IntraHostHIVenvLiao2013_NoStop_subalign.fasta -T AUTO -pre ./IntraHostHIVenvLiao2013_NoStop_subalign.fasta 
```

We will visualize the tree with **FigTree** and save Newick file

#### 3. Selection Analysis

To get exposed to selection analysis we will do a short exercise using Hyphy. We will use a version of the previous alignment that was modified to be in-frame.

Upload the Newick file: IntraHostHIVenvLiao2013_NoStop_subalign.fasta.treefile.nwk

```
hyphy meme --alignment /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/IntraHostHIVenvLiao2013_NoStop_subalign.fasta --tree /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/IntraHostHIVenvLiao2013_NoStop_subalign.fasta.treefile.nwk --output hyphy_meme_IntraHostHIVenvLiao2013_NoStop_subalign
```
We can visualize the results using http://vision.hyphy.org


Alternatively, we can use the Datamonkey server:

  1. Go to: http://www.datamonkey.org/

  2. Select Episodic Site Selction Analysis (MEME)

  3. Upload File: IntraHostHIVenvLiao2013_NoStop_subalign.fasta

  4. Check results and identify sites under positive selection. 

We will view the alignment using the **Highlighter** tool from the HIV sequence database from Los Alamos.

  1. Go to: https://www.hiv.lanl.gov/content/sequence/HIGHLIGHT/highlighter_top.html

  2. Upload File: IntraHostHIVenvLiao2013_NoStop_subalign.fasta

#### 4. Phylodynamics

We will apply simple concepts of coalescence and phylodynamics to analyze the origin of the BA.4 lineage of SARS-CoV-2. We will use the alignment generated in **Exercise 1**. We will use TreeTime to perform this analysis.

  1. Check the alignment and assess QC with **SeaView**

  2. Generate an ML tree as in **Exercise 2**
```
iqtree2 -s /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/SARSCoV2_BA4_Aligned_NoRef.fasta -T AUTO -alrt 1000 -pre ./SARSCoV2_BA4_Aligned_NoRef.fasta
```
  3. Use the output and metadata including dates to generate a temporal tree and coalescence analysis using ML approach.
  
```
treetime --confidence --relax 1.0 0.5 --aln /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/SARSCoV2_BA4_Aligned_NoRef.fasta --tree SARSCoV2_BA4_Aligned_NoRef.fasta.treefile --dates SARSCoV2_BA4.metadata.tsv --coalescent skyline --clock-filter 4 --clock-rate 0.0008 --clock-std-dev 0.0004 --branch-length-mode marginal --outdir timetree_out
```


  4. Perform ML ancestral state reconstruction to analyze phylogeography.

```
treetime mugration --tree timetree_out/timetree.nexus --states /projects/e30682/Phylogenetics_DGP485_Materials_Lorenzo/SARSCoV2_BA4.metadata.tsv --attribute country --outdir mugration_out
```

We will visualize the tree and perform initial analysis with **FigTree**

I recommend to check ggtree for better visualization options: https://yulab-smu.top/treedata-book/
