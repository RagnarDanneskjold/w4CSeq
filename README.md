![w4CSeq](/html/W4CSEQ_files/w4cseq.png)
# w4CSeq: software and web application to analyze 4C-Seq data

w4CSeq applies a customized pipeline to deal with both enzyme digestion and sonication fragmentation based 4C-Seq data. 
It identifies 4C sites, statistically significant regions, draws interacting plots for intra-chromosomal and inter-chromosomal interactions, and overlays genomic features (TSS, TTS, CpG sites), DNA replication timing and user-provided annotation onto it. 
The combined plots will help uncover significant features in/around 4C regions. 

# Features
* Web server with simple input specification and multiple outputs.
* Users can download the software and install their own servers in a local environment, or use command line to perform analysis.
* Able to analyze 4C-Seq data generated by both enzyme-digestion and sonication-fragmentation methods.

# Usage
## 1. Server
### *demo web server*
* We provide a demo server [w4CSeq](http://w4cseq.wglab.org/) for you to examine. 

### *build your own server*

##### a. Download and Install
Users can download the cutting edge version from GitHub by `git clone git@github.com:WGLab/w4CSeq.git`.

##### b. Software prerequisite
The following softwares should be installed in your cluster before you build server or run w4CSeq command line.
  * R (Packages: RCircos, quantsmooth)
  * Perl (Module: Math::CDF, List::Util)
  * BWA
  * SAMtools
  * BEDTools

##### c. Build `lib` directory
To build the required `lib` directory, users can download `lib.tar.gz` file from [here](http://w4cseq.wglab.org/W4CSEQ_files/lib.tar.gz).
Then extract files under `/w4cseq/` directory by `tar -zxvf lib.tar.gz -C /PATH/TO/w4cseq/`. After that, there will be four sub-directories (`bin`, `cgi-bin`, `html`, `lib`) under `/w4cseq/`, and four sub-sub-directories (`hg19`, `hg18`, `mm10`, and `mm9`) under `/w4cseq/lib/`. 

Each sub-directory (`hg19`, `hg18`, `mm10`, and `mm9`) under `/w4cseq/lib/` consists of genome sequence and index files (for BWA alignment); annotation files for TTSs, TSSs, CpG sites and genes; reduced library of genome-wide enzyme recognition sites; DNA replication timing annotation files; and cytoband definition file. 

##### d. Specify paths of softwares in program
  * ##### Enzyme-digestion based 4C-Seq 
    * In `4C_enzyme.R`under `/w4cseq/bin/`, 1) specify the following (`path_w4CSeq`, `path_bwa`, `path_samtools`, `path_bedtools`, `path_RCircos`, `path_quantsmooth`), representing your paths to `w4CSeq`, `BWA`, `SAMtools`, `BEDTools`, `RCircos` and `quantsmooth` respectively; 2) specify your interpreter in the very first `#!` line. 
    * In `Enzyme.cgi` under `/w4cseq/cgi-bin/` and `control_w4cseq_enzyme.pl` under `/w4cseq/bin/`, specify the following (`$CARETAKER`, `$SERVER_DIRECTORY`, `$WEBSITE`), representing `email` of housekeeper, path to `w4CSeq`, and assigned website address for your server respectively.

  * ##### Sonication-digestion based 4C-Seq 
    * In `4C_sonication.R`under `/w4cseq/bin/`, 1) specify the following (`path_w4CSeq`, `path_bwa`, `path_samtools`, `path_bedtools`, `path_RCircos`, `path_quantsmooth`); 2) specify your interpreter in the very first `#!` line.
    * In `Sonication.cgi` under `/w4cseq/cgi-bin/` and `control_w4cseq_sonication.pl` under `/w4cseq/bin/`, specify the following (`$CARETAKER`, `$SERVER_DIRECTORY`, `$WEBSITE`).

Once these are set up, the server is ready to go.


## 2. Command line
We recommend you build your own server since it will take input and yield output in an automated manner. Alternatively, you can use one-line command to analyze your 4C-Seq data.

##### a. Complete the `a`, `b`, and `c` steps in `1. Server` section.

##### b. We suggest you perform all analysis under `/w4cseq/work/` directory (if it doesn't exist, creat one by `mkdir /w4cseq/work`), and set separate subdirectory to store outputs for each analysis for clean organization.

  * Enzyme digestion based 4C-Seq data analysis
 
In `enzyme_config.r`under `/w4cseq/bin/`, 1) specify the following (`path_w4CSeq`, `path_bwa`, `path_samtools`, `path_bedtools`, `path_RCircos`, `path_quantsmooth`); 2) specify your interpreter in the very first `#!` line; 3) specify the following parameters:
  1. **proc**: number of threads. 1 by default and applicable to BWA alignment
  2. **exp_name**: name of folder to create and store files
  3. **file_in**: path to the raw fastq file
  4. **unzip**: whether your data is uncompressed ("yes"/"no")
  5. **build**: reference genome
  6. **primer_frag**: primer sequence for bait region
  7. **enzyme**: recognition sequence for primary restriction enzyme
  8. **bait_ch**: bait chromosome
  9. **bait_st**: starting position of primer pair
  10. **bait_en**: ending position of primer pair
  11. **size_inter**: bin size for *trans* chromosome (count of enzyme sites in foreground window)
  12. **size_intra**: bin size for *cis* chromosome (count of enzyme sites in foreground window)
  13. **window_intra**: window size for *cis* chromosome (count of enzyme sites in background window)
  14. **FDR**: FDR threshold

In `count_sites_binomial.pl` under `/w4cseq/bin/scripts/`, 1) specify your interpreter in the very first `#!` line, such as `#!/usr/bin/perl`; 2) specify the directory in which your perl modules are installed.


Then run the command line as follows (`enzyme_config.r` will be sourced automatically):
```
4C_enzyme_cmdline.R
```

  * Sonication fragmentation based 4C-Seq data analysis
 
In `sonication_config.r` under `/w4cseq/bin/`, 1) specify the following (`path_w4CSeq`, `path_bwa`, `path_samtools`, `path_bedtools`, `path_RCircos`, `path_quantsmooth`); 2) specify your interpreter in the very first `#!` line; 3) specify the following parameters:
  1. **proc**: number of threads. 1 by default and applicable to BWA alignment
  2. **exp_name**: name of folder to create and store files
  3. **file_in1**: path to the paired end raw fastq files #1
  4. **file_in2**: path to the paired end raw fastq files #2
  5. **unzip**: whether your data is uncompressed ("yes"/"no")
  6. **build**: reference genome
  7. **bait_ch**: bait chromosome
  8. **bait_st**: starting position of primer pair
  9. **bait_en**: ending position of primer pair
  10. **extend**: Extended length from end of primer pair to define "bait" neighborhood
  11. **size_inter**: bin size for *trans* chromosome (bp length in foreground window)
  12. **size_intra**: bin size for *cis* chromosome (bp length in foreground window)
  13. **window_intra**: window size for *cis* chromosome (bp length in background window)
  14. **FDR**: FDR threshold

In `positive_region_binomial.pl` under `/w4cseq/bin/scripts/`, 1) specify your interpreter in the very first `#!` line, such as `#!/usr/bin/perl`; 2) specify the directory in which your perl modules are installed.

Then run the command line as follows (`sonication_config.r` will be sourced automatically):
```
4C_sonication_cmdline.R
```

#Output
w4CSeq provides multiple outputs for you. Please refer to [demo](http://w4cseq.usc.edu/done/827/vH_Y7YpVpXVak9Fk/index.html) result for enzyme-digestion based 4C-Seq result and [demo](http://w4cseq.usc.edu/done/829/2QlTqIO_4FVuKid-/index.html) result for sonication-fragmentation based 4C-Seq result.

If you use command line tool to analyze data, output files are stored in the sub-directory you specified. The descriptions for important files are listed below.

Tabular files:
1. **DISTAL_INTERACTION_SITES.bed**: This file contains all the restriction sites that have interacting signals covered. The first three columns characterize the location of a covered restriction site and the fourth column is the number of reads mapped there.
2. **DISTAL_INTERACTION_SITES_pValue.bed**: This file contains the restriction sites with the fourth column representing the p-values after modeling.
3. **DISTAL_INTERACTION_SITES_pValue_adjusted.bed**: This file contains the restrction sites with the fourth column representing the p-values adjusted using BH/FDR method.
4. **positive_hits.bed**: This file contains the interacting sites with adjusted p-value less than specified significance threshold (e.g. 0.05). 
5. **SIGNIFICANT_REGIONS.bed**: This file contains significant regions where neighbouring significant sites are close. (Each site is extended into a window with size of size_inter (trans) or size_intra (cis). Those sites with overlapping windows are merged into domains and boundaries of domains are documented.)

Files for visualization in genome browsers:
1. **MAPPED_BAM.bam**: File of mapped reads. Note that it contains PCR artifacts so caution need to be exercised. **MAPPED_BAM.bam.bai** is the index file required to be loaded together with the bam file.
2. **UCSC_view.bed**: File characterizing raw reads pile-up. It can be examined in UCSC genome browser.
3. **window.bed**: File characterizing the captured sites summed in smoothing window. It can be examined in UCSC genome browser.

Figures:
1. **genome.png\pdf**: Genome wide‐distribution of 4C regions as indicated by red rectangles on top of chromosome ideogram.
2. **circos.png\pdf**: Circos plot of genome‐wide distribution of 4C interactions as indicated by curves extended from the ‘bait’ region. Intra-chromosomal (cis) and inter-chromosomal (trans) interactions are shown in red and blue, respectively.
3. **spider.png\pdf**: Spider plot depicting contacts centered on the ‘bait’ region.
4. **domainogram.png\pdf**: Domainogram depicting interaction intensities across window size ranging from 2 to 200 restriction sites.
5. **distance.png\pdf**: Density curve showing relative distance of 4C sites from key genomic features including reference genes, TSSs, TTSs and CpG islands compared to random.
6. **DNA_replication.png\pdf**: Box plot showing the distribution of DNA replication timing values of 4C regions compared to the whole genome in 10 pluripotent cell lines. Early replication domains have the logarithm of replication timing ratio > 0.


Fore more information on the parameters settings and other, see [here](http://w4cseq.wglab.org).


# Contact
Mingyang Cai

caim@usc.edu


# More
* [w4CSeq Homepage](http://w4cseq.wglab.org)
* [Wang Genomics Lab Homepage](http://genomics.usc.edu)

