### Downloading data
Command used for downloading 1M of records from SRR064545, SRR064546, SRR064547 runs:  
*fastq-dump --split-3 -X 1000000 SRR064545*  

### Quality analysis:
Command used for quality analysis:  
*fastqc SRR064545_1.fastq SRR064545_2.fastq  SRR064546_1.fastq SRR064546_2.fastq  SRR064547_1.fastq SRR064547_2.fastq*

### Sequence trimming:
Command used for trimming low quality bases (for each record):  
**java -jar** */trimmomatic-0.40-rc1.jar PE SRR064547_1.fastq SRR064547_2.fastq SRR064547_1_paired.fastq SRR064547_1_unpaired.fastq SRR064547_2_paired.fastq SRR064547_2_unpaired.fastq* **ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:20 MINLEN:22**

### Reference genome:
Database used for aquiring reference genome:  
*/ensemblgenomes/pub/release-53/fungi/fasta/saccharomyces_cerevisiae/dna/*

### Aligment to reference genome:
**bwa index** *Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa*.  
**bwa mem** *Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa SRR064545_1_paired.fastq SRR064545_2_paired.fastq > aln2_4545.sam*

### Changing file format from sam to bam:  
**samtools view** *-S -b aln_4545.sam > aln_4545.bam*

### Post-aligment processing:
**samtools fixmate** *-r -m aln_4545.bam aln_4545_fix.bam*  
**samtools sort** *aln_4545_fix.bam -o aln_4545_sorted.bam* 
**samtools markdup** *-r aln_4545_sorted.bam aln_4545_marked.bam* 
**samtools index** *aln_4545_marked.bam*   

### Aligment statistics:
**samtools flagstat** *aln_4545_marked.bam > aln_4545_flagstat.txt*

### Polymorphism detection:
**bcftools mpileup -f** */genome_reference/Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa aln_4545_marked.bam* | **bcftools call** *-mv -Ob -o polymorphism_4545.bcf*  
**bcftools convert** *polymorphism_4547.bcf -o polymorphism4547.vcf*
