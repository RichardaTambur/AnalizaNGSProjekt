###Downloading data

Command used for downloading 1M of records from SRR064545, SRR064546, SRR064547 runs:
*fastq-dump --split-3 -X 1000000 SRR064545*  

Command used for quality analyze:
*fastqc SRR064545_1.fastq SRR064545_2.fastq  SRR064546_1.fastq SRR064546_2.fastq  SRR064547_1.fastq SRR064547_2.fastq*


Command used for trimming low quality bases (for each record):
java -jar */trimmomatic-0.40-rc1.jar PE */data/raw_data/SRR064547_1.fastq */data/raw_data/SRR064547_2.fastq SRR064547_1_paired.fastq SRR064547_1_unpaired.fastq SRR064547_2_paired.fastq SRR064547_2_unpaired.fastq ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:20 MINLEN:22
java -jar /Users/togepi/Desktop/Analiza_NGS_Projekt/gatk-4.2.6.1/gatk-package-4.2.6.1-local.jar BaseRecalibrator -I /Users/togepi/Desktop/Analiza_NGS_Projekt/AnalizaNGSProjekt/data/aligments/aligments_mu/aln_4545_mu.bam.bai -R /Users/togepi/Desktop/Analiza_NGS_Projekt/AnalizaNGSProjekt/data/genome_reference/Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa
java -jar /Users/togepi/Desktop/Analiza_NGS_Projekt/gatk-4.2.6.1/gatk-package-4.2.6.1-local.jar  CreateSequenceDictionary -R /Users/togepi/Desktop/Analiza_NGS_Projekt/AnalizaNGSProjekt/data/genome_reference/Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa
Database used for aquiring reference genome:
/ensemblgenomes/pub/release-53/fungi/fasta/saccharomyces_cerevisiae/dna/

Aligment script:
bwa index Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa
bwa mem data/genome_reference/Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa data/strip_data/4547/SRR064547_1_paired.fastq data/strip_data/4547/SRR064547_2_paired.fastq > aln_4547.sam
bwa mem Saccharomyces_cerevisiae.R64-1-1.dna.chromosom.fa SRR064547_1_paired.fastq SRR064547_2_paired.fastq > aln2_4547.sam

Format sam to bam:
samtools view -S -b aln_4545.sam > aln_4545.bam

Post-aligment processing:
samtools fixmate -r -m aln_4545.bam aligments_fixed/aln_4545_fix.bam
samtools sort aln_4545_fix.bam -o aln_4545_sorted.bam
samtools markdup -r aln_4547_sorted.bam aln_4547_mu.bam
samtools index aln_4547_mu.bam

Showing stats:
samtools flagstat aln_4547_mu.bam

4545 mapping results:
1381696 + 0 in total (QC-passed reads + QC-failed reads)
1381696 + 0 primary
0 + 0 secondary
0 + 0 supplementary
0 + 0 duplicates
0 + 0 primary duplicates
1381696 + 0 mapped (100.00% : N/A)
1381696 + 0 primary mapped (100.00% : N/A)
1368300 + 0 paired in sequencing
684150 + 0 read1
684150 + 0 read2
1063914 + 0 properly paired (77.75% : N/A)
1368300 + 0 with itself and mate mapped
0 + 0 singletons (0.00% : N/A)
12784 + 0 with mate mapped to a different chr
10171 + 0 with mate mapped to a different chr (mapQ>=5)
(base) ➜  aligments_mu git:(main) ✗ 

4546 mapping results:
1558578 + 0 in total (QC-passed reads + QC-failed reads)
1558578 + 0 primary
0 + 0 secondary
0 + 0 supplementary
0 + 0 duplicates
0 + 0 primary duplicates
1558578 + 0 mapped (100.00% : N/A)
1558578 + 0 primary mapped (100.00% : N/A)
1536291 + 0 paired in sequencing
768140 + 0 read1
768151 + 0 read2
771654 + 0 properly paired (50.23% : N/A)
1536291 + 0 with itself and mate mapped
0 + 0 singletons (0.00% : N/A)
33016 + 0 with mate mapped to a different chr
25466 + 0 with mate mapped to a different chr (mapQ>=5)

4547 mapping results:
1406900 + 0 in total (QC-passed reads + QC-failed reads)
1406900 + 0 primary
0 + 0 secondary
0 + 0 supplementary
0 + 0 duplicates
0 + 0 primary duplicates
1406900 + 0 mapped (100.00% : N/A)
1406900 + 0 primary mapped (100.00% : N/A)
1372852 + 0 paired in sequencing
686426 + 0 read1
686426 + 0 read2
0 + 0 properly paired (0.00% : N/A)
1372852 + 0 with itself and mate mapped
0 + 0 singletons (0.00% : N/A)
34556 + 0 with mate mapped to a different chr
25549 + 0 with mate mapped to a different chr (mapQ>=5)


