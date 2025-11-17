EXAMEN BIOINFORMATIQUE MAÊLYS CHAPIS & MILA TRIPON
# Vérification du directory
pwd

# Part 1 — Linux basics 

Create a working directory called finalexam on your student account, in your $HOME directory. Copy the dataset from filesender link above.

e22402344@ed55:~$ mkdir finalexam
e22402344@ed55:~$ scp SRR034310_10pc.fastq /home/2025LBISM2/e22402344/finalexam
e22402344@ed55:~$ scp Details_Barcode_Population_SRR034310.txt /home/2025LBISM2/e22402344/finalexam
e22402344@ed55:~$ scp Reference_genome_chrI.fasta /home/2025LBISM2/e22402344/finalexam

Explore file content with bash commands using and report: 
How many lines are in the fastq file?
wc -l < SRR034310_10pc.fastq

What is the read length?
22402344@ed55:~$ nseq=$((`wc -l < SRR034310_10pc.fastq / 4))
e22402344@ed55:~$ echo $nseq
897003

How many barcodes are in Details_Barcode_Population_SRR034310?
wc -l < Details_Barcode_Population_SRR034310.txt
16


#Partie 2
Run FastQC on the fastq file.

home/2025LBISM2/e22402344/fastqc_v0.12.1/FastQC/./fastqc $1

# résultats html

sequence length distribution
pic autour de 36 pb toutes noss séquences de la meme longuer
    
quality drop at 5' and 3' ends of reads (if any)
qualité bonne entre 11 et 22 pb 
oui on a un drop 
    
pas de présence d'adaptateurs
    
overrepresented sequences
3 séquences 

which restriction enzyme was used to create these data?

sbf1, regarde par rapport au pourcentage de atcg dans per base sequence content 
TGCA GG qui correspond à  sbf1

what is the 4 nt sequence preceeding the enzyme overhang?
TGCA

# Part 3 — Demultiplexing using barcodes

Using simple grep and sed, create 16 new FASTQ files, one for each sample, based on Details_Barcode_Population_SRR034310. Name the demultiplexed files as, for ex, BearPaw1.fastq (for barcode CCCC). 

grep -B1 -A2 "^CCAA" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw2.fastq
grep -B1 -A2 "^CCCC" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw1.fastq 
grep -B1 -A2 "^CCTT" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw3.fastq
grep -B1 -A2 "^CCGG" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw4.fastq 
grep -B1 -A2 "^CACA" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw5.fastq 
grep -B1 -A2 "^CAAC" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw6.fastq
grep -B1 -A2 "^CATG" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw7.fastq
grep -B1 -A2 "^CAGT" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > BearPaw8.fastq 
grep -B1 -A2 "^CTCT" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough1.fastq 
grep -B1 -A2 "^CTAG" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough2.fastq
grep -B1 -A2 "^CTTC" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough3.fastq
grep -B1 -A2 "^CTGA" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough4.fastq 
grep -B1 -A2 "^GGGG" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough5.fastq 
grep -B1 -A2 "^GGAA" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough6.fastq 
grep -B1 -A2 "^GGTT" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough7.fastq 
grep -B1 -A2 "^GGCC" SRR034310_10pc.fastq| sed 's/@SRR/@SRR/g' > RabbitSlough8.fastq 


compter le nombre de reads 
grep "^>SRR" BearPaw1.fastq | wc -l
15802
grep "^>SRR" BearPaw2.fastq | wc -l
15754
grep "^>SRR" BearPaw3.fastq | wc -l
15314
grep "^>SRR" BearPaw4.fastq | wc -l
23119
grep "^>SRR" BearPaw5.fastq | wc -l
24149
grep "^>SRR" BearPaw6.fastq | wc -l
67266
grep "^>SRR" BearPaw7.fastq | wc -l
44927
grep "^>SRR" BearPaw8.fastq | wc -l
68112
grep "^>SRR" RabbitSlough1.fastq | wc -l
37965
grep "^>SRR" RabbitSlough2.fastq | wc -l
92388
grep "^>SRR" RabbitSlough3.fastq | wc -l
34596
grep "^>SRR" RabbitSlough4.fastq | wc -l
34087
grep "^>SRR" RabbitSlough5.fastq | wc -l
136019
grep "^>SRR" RabbitSlough6.fastq | wc -l
64961
grep "^>SRR" RabbitSlough7.fastq | wc -l
98983
grep "^>SRR" RabbitSlough8.fastq | wc -l
55038

puis enlever les barcodes:

seqtk trimfq -b 4 BearPaw1.fastq > BearPaw1trim.fastq
seqtk trimfq -b 4 BearPaw2.fastq > BearPaw2trim.fastq
seqtk trimfq -b 4 BearPaw3.fastq > BearPaw3trim.fastq
seqtk trimfq -b 4 BearPaw4.fastq > BearPaw4trim.fastq
seqtk trimfq -b 4 BearPaw5.fastq > BearPaw5trim.fastq
seqtk trimfq -b 4 BearPaw6.fastq > BearPaw6trim.fastq
seqtk trimfq -b 4 BearPaw7.fastq > BearPaw7trim.fastq
seqtk trimfq -b 4 BearPaw8.fastq > BearPaw8trim.fastq
seqtk trimfq -b 4 RabbitSlough1.fastq > RabbitSlough1trim.fastq
seqtk trimfq -b 4 RabbitSlough2.fastq > RabbitSlough2trim.fastq
seqtk trimfq -b 4 RabbitSlough3.fastq > RabbitSlough3trim.fastq
seqtk trimfq -b 4 RabbitSlough4.fastq > RabbitSlough4trim.fastq
seqtk trimfq -b 4 RabbitSlough5.fastq > RabbitSlough5trim.fastq
seqtk trimfq -b 4 RabbitSlough6.fastq > RabbitSlough6trim.fastq
seqtk trimfq -b 4 RabbitSlough7.fastq > RabbitSlough7trim.fastq
seqtk trimfq -b 4 RabbitSlough8.fastq > RabbitSlough8trim.fastq

# Part 4 — Mapping to a reference genome

index réference
bwa-mem2 index Reference_genome_chrI.fasta

bwa-mem2 mem Reference_genome_chrI.fasta BearPaw1trim.fastq > BearPaw1.sam
bwa-mem2 mem Reference_genome_chrI.fasta BearPaw2trim.fastq > BearPaw2.sam
bwa-mem2 mem Reference_genome_chrI.fasta BearPaw3trim.fastq > BearPaw3.sam
bwa-mem2 mem Reference_genome_chrI.fasta BearPaw4trim.fastq > BearPaw4.sam
bwa-mem2 mem Reference_genome_chrI.fasta BearPaw5trim.fastq > BearPaw5.sam
bwa-mem2 mem Reference_genome_chrI.fasta BearPaw6trim.fastq > BearPaw6.sam
bwa-mem2 mem Reference_genome_chrI.fasta BearPaw7trim.fastq > BearPaw7.sam
bwa-mem2 mem Reference_genome_chrI.fasta BearPaw8trim.fastq > BearPaw8.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough1trim.fastq > RabbitSlough1.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough2trim.fastq > RabbitSlough2.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough3trim.fastq > RabbitSlough3.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough4trim.fastq > RabbitSlough4.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough5trim.fastq > RabbitSlough5.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough6trim.fastq > RabbitSlough6.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough7trim.fastq > RabbitSlough7.sam
bwa-mem2 mem Reference_genome_chrI.fasta RabbitSlough8trim.fastq > RabbitSlough8.sam

samtools view -bS BearPaw1.sam > BearPaw1.bam
samtools sort BearPaw1.bam -o BearPaw1_sorted.bam
samtools index BearPaw1_sorted.bam

samtools view -bS BearPaw2.sam > BearPaw2.bam
samtools sort BearPaw2.bam -o BearPaw2_sorted.bam
samtools index BearPaw2_sorted.bam

samtools view -bS BearPaw3.sam > BearPaw3.bam
samtools sort BearPaw3.bam -o BearPaw3_sorted.bam
samtools index BearPaw3_sorted.bam

samtools view -bS BearPaw4.sam > BearPaw4.bam
samtools sort BearPaw4.bam -o BearPaw4_sorted.bam
samtools index BearPaw4_sorted.bam

samtools view -bS BearPaw5.sam > BearPaw5.bam
samtools sort BearPaw5.bam -o BearPaw5_sorted.bam
samtools index BearPaw5_sorted.bam

samtools view -bS BearPaw6.sam > BearPaw6.bam
samtools sort BearPaw6.bam -o BearPaw6_sorted.bam
samtools index BearPaw6_sorted.bam

samtools view -bS BearPaw7.sam > BearPaw7.bam
samtools sort BearPaw7.bam -o BearPaw7_sorted.bam
samtools index BearPaw7_sorted.bam

samtools view -bS BearPaw8.sam > BearPaw8.bam
samtools sort BearPaw8.bam -o BearPaw8_sorted.bam
samtools index BearPaw8_sorted.bam

samtools view -bS RabbitSlough1.sam > RabbitSlough1.bam
samtools sort RabbitSlough1.bam -o RabbitSlough1_sorted.bam
samtools index RabbitSlough1_sorted.bam

samtools view -bS RabbitSlough2.sam > RabbitSlough2.bam
samtools sort RabbitSlough2.bam -o RabbitSlough2_sorted.bam
samtools index RabbitSlough2_sorted.bam

samtools view -bS RabbitSlough3.sam > RabbitSlough3.bam
samtools sort RabbitSlough3.bam -o RabbitSlough3_sorted.bam
samtools index RabbitSlough3_sorted.bam

samtools view -bS RabbitSlough4.sam > RabbitSlough4.bam
samtools sort RabbitSlough4.bam -o RabbitSlough4_sorted.bam
samtools index RabbitSlough4_sorted.bam

samtools view -bS RabbitSlough5.sam > RabbitSlough5.bam
samtools sort RabbitSlough5.bam -o RabbitSlough5_sorted.bam
samtools index RabbitSlough5_sorted.bam

samtools view -bS RabbitSlough6.sam > RabbitSlough6.bam
samtools sort RabbitSlough6.bam -o RabbitSlough6_sorted.bam
samtools index RabbitSlough6_sorted.bam

samtools view -bS RabbitSlough7.sam > RabbitSlough7.bam
samtools sort RabbitSlough7.bam -o RabbitSlough7_sorted.bam
samtools index RabbitSlough7_sorted.bam

samtools view -bS RabbitSlough8.sam > RabbitSlough8.bam
samtools sort RabbitSlough8.bam -o RabbitSlough8_sorted.bam
samtools index RabbitSlough8_sorted.bam




scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/*.bam' ~ /home/2025LBISM2/e22402344/


samtools flagstat BearPaw1_sorted.bam
samtools flagstat BearPaw2_sorted.bam
samtools flagstat BearPaw3_sorted.bam
samtools flagstat BearPaw4_sorted.bam
samtools flagstat BearPaw5_sorted.bam
samtools flagstat BearPaw6_sorted.bam
samtools flagstat BearPaw7_sorted.bam
samtools flagstat BearPaw8_sorted.bam
samtools flagstat RabbitSlough1_sorted.bam
samtools flagstat RabbitSlough2_sorted.bam
samtools flagstat RabbitSlough3_sorted.bam
samtools flagstat RabbitSlough4_sorted.bam
samtools flagstat RabbitSlough5_sorted.bam
samtools flagstat RabbitSlough6_sorted.bam
samtools flagstat RabbitSlough7_sorted.bam
samtools flagstat RabbitSlough8_sorted.bam


Mapping stats summarized:
        % mapped

        number of reads
        number of duplicates (if info available)

    5–6 line interpretation:
        What does mapping % tell you?
        Why do marine genomes often produce lower mapping % (repeats, divergence, etc.)?


# Part 5 — Calling SNPs

samtools faidx Reference_genome_chrI.fasta 

mkdir vcf
cd vcf


bcftools mpileup -Ou -f ../Reference_genome_chrI.fasta ../BearPaw1_sorted.bam ../BearPaw2_sorted.bam | bcftools call -mv -Ov -o raw_variants.vcf 

vcftools --vcf raw_variants.vcf --freq 

After filtering, kept 17 out of a possible 17 Site

vcftools --vcf raw_variants.vcf --minDP 5 --max-missing 1 --min-alleles 2 --max-alleles 2 --recode --out filtered 

Il en reste 5
mindp 5 PROFONDEUR DE lecture de au moins 5
QUE LES SNP PRÉSENTES Et chez tous les individus



scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/vcf/*.frq' ~ /home/2025LBISM2/e22402344/

scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/vcf/*.vcf' ~ /home/2025LBISM2/e22402344/

0638484675


bcftools mpileup -Ou -f ../Reference_genome_chrI.fasta ../BearPaw1_sorted.bam ../BearPaw2_sorted.bam ../BearPaw3_sorted.bam ../BearPaw4_sorted.bam ../BearPaw5_sorted.bam ../BearPaw6_sorted.bam ../BearPaw7_sorted.bam ../BearPaw8_sorted.bam ../RabbitSlough1_sorted.bam ../RabbitSlough2_sorted.bam ../RabbitSlough3_sorted.bam ../RabbitSlough4_sorted.bam ../RabbitSlough5_sorted.bam ../RabbitSlough6_sorted.bam ../RabbitSlough7_sorted.bam ../RabbitSlough8_sorted.bam | bcftools call -mv -Ov -o raw_variants.vcf

vcftools --vcf raw_variants.vcf --freq  --out allele_freqs

127 snp
4 filtrés

  ../RabbitSlough8_sorted.bam


vcftools --vcf raw_variants.vcf --weir-fst-pop popbear.txt --weir-fst-pop poprabbit.txt --out fst_pop
