
# Analysis’s steps:

1. Downloading data for the research from SRA repository
2. Checking the quality of the files with fastqc
3. Trimming the sequences by using trimmomatic
4. Checking the quality after trimming by fastqc
5. Aquiring the reference genome from ensemble fungi database
6. Aligment to reference genome with Burrows-Wheeler Aligner
7. Post-aligment processing with samtools package
8. Polymorphism detection with bcftools
9. Generating results in VPC format with Variant Effect Predictor.
10. Analysis of results 

## Data:

Data used for the experiment was downloaded from https://trace.ncbi.nlm.nih.gov/Traces/sra/?study=SRP003355.  
There were mentioned three different yeast strains: wild type VAC22 to demonstrate the detection of structural variants, VAC6 - wild type before mutation and VAC6 mutated.  
In the research from which data was taken the goal of next generation sequencing was to determine functional mutations from a large excess of polymorphisms, incidental mutations and other sequencing errors.  
The informations were stored in pair-end fasta files.

## Used commands:

### - Downloading data
Command used for downloading 1M of records from SRR064545, SRR064546, SRR064547 runs:  
*fastq-dump --split-3 -X 1000000 SRR064545*  

### - Quality analysis
Command used for quality analysis:  
*fastqc SRR064545_1.fastq SRR064545_2.fastq  SRR064546_1.fastq SRR064546_2.fastq  SRR064547_1.fastq SRR064547_2.fastq*

### - Sequence trimming
Command used for trimming low quality bases (for each record):  
**java -jar** */trimmomatic-0.40-rc1.jar PE SRR064547_1.fastq SRR064547_2.fastq SRR064547_1_paired.fastq SRR064547_1_unpaired.fastq SRR064547_2_paired.fastq SRR064547_2_unpaired.fastq* **ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:20 MINLEN:22**

### - Reference genome
Database used for aquiring reference genome:  
*/ensemblgenomes/pub/release-53/fungi/fasta/saccharomyces_cerevisiae/dna/*

### - Aligment to reference genome
**bwa index** *Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa*.  
**bwa mem** *Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa SRR064545_1_paired.fastq SRR064545_2_paired.fastq > aln2_4545.sam*

### - Changing file format from sam to bam 
**samtools view** *-S -b aln_4545.sam > aln_4545.bam*

### - Post-aligment processing
**samtools fixmate** *-r -m aln_4545.bam aln_4545_fix.bam*  
**samtools sort** *aln_4545_fix.bam -o aln_4545_sorted.bam* 
**samtools markdup** *-r aln_4545_sorted.bam aln_4545_marked.bam* 
**samtools index** *aln_4545_marked.bam*   

### - Aligment statistics
**samtools flagstat** *aln_4545_marked.bam > aln_4545_flagstat.txt*

### - Polymorphism detection
**bcftools mpileup -f** */genome_reference/Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa aln_4545_marked.bam* | **bcftools call** *-mv -Ob -o polymorphism_4545.bcf*  
**bcftools convert** *polymorphism_4547.bcf -o polymorphism4547.vcf*
  
  
Operations of:  
  
FASTA 
To download the files from SRA database fastdump was used, because it has an option for determining how much reads we want to get. Flag split-3 was added, so in the result we got two files from both strands.  

FASTQC  
On fasta files freshly downloaded from SRA database I performed quality tests using fastqc, from which I could read quality scores. Per base sequence quality showed that many bases of the sequences had quality lower than 20, so they needed to be cutted.  

FASTQ  
The output of trimmomatic program, which was used to trim bases with low quality, was two fastq files for each sequence. The encoding was Illumina 1.9 what we needed to mention in the command line, as well with information how big we want our sliding window to be and what was the minimal lenght of the sequences which we wanted to spare after cutting. First file, paired gave us info about which sequences from both strands survived and unpaired where the ones that was disqualified. For further operations only the paired files were used. 

FASTA  
Reference genome was downloaded in parts, separate file for each chromosome. I used *cat > /* command to concatenate them all into one, so it could be used for the aligment.
  
SAM/BAM
I aligned the paired sequences to reference genome using bwa program. The sam format is a bit too big for being stored, so it was immediately transformed into BAM format, which is more sufficient for next operations. For next tools, bam file needed to be indexed, sorted and had marked duplicates, so it could be used for the detection of polymorphism.

TXT
In txt format I have saved statistincs after aligment, which were performed with samtools flagstat
