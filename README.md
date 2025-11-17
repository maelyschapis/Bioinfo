EXAMEN BIOINFORMATIQUE MAÊLYS CHAPIS & MILA TRIPON
# Vérification du directory et création dossier finalexam
pwd

# Part 1 — Linux basics 
## 1) Create a working directory called finalexam on your student account, in your $HOME directory. Copy the dataset from filesender link above.

```
kdir finalexam
scp SRR034310_10pc.fastq /home/2025LBISM2/e22402344/finalexam
scp Details_Barcode_Population_SRR034310.txt /home/2025LBISM2/e22402344/finalexam
scp Reference_genome_chrI.fasta /home/2025LBISM2/e22402344/finalexam
```

## 2) Explore file content with bash commands using and report: 
cat SRR034310_10pc.fastq | head # pour voir à quoi ressemble le fichier

### 2.1) How many lines are in the fastq file?
wc -l < SRR034310_10pc.fastq # IL Y A 356412

### 2.2) What is the read length?
22402344@ed55:~$ nseq=$((`wc -l < SRR034310_10pc.fastq / 4))
e22402344@ed55:~$ echo $nseq # Il y a 897003 séquences dans ce fichier

### 2.3) How many barcodes are in Details_Barcode_Population_SRR034310?
wc -l < Details_Barcode_Population_SRR034310.txt # Il y a 16 barcodes


# Part 2 — Quality control 
home/2025LBISM2/e22402344/fastqc_v0.12.1/FastQC/./fastqc $1

# résultats html
### 1.1) Describe : sequence length distribution, quality drop at 5' and 3' ends of reads (if any), presence of adapters and overrepresented sequences
#### POUR SEQUENCE LENGHT DISTRIBUTION : Il y a une valeur maxaimale autour de 36 pb. Toutes nos séquences sont de la même longueur. 
#### POUR QUALITY DROP AT 5' AND 3' ENDS OF READS : La qualité est bonne entre 11 et 22 pb. Nous avons donc bien un drop. 
#### POUR LA PRESENCE D'ADAPTATEURS : Nous n'avons pas de présence d'adaptateur.     
#### POUR L'OVERREPRESENTED SEQUENCES : Nous avons 3 séquences listées et sur-représentées dans notre fichier de données.  

#### file:///home/2025LBISM2/e22402344/qc/SRR034310_10pc_fastqc.html

### 1.2) which restriction enzyme was used to create these data?
#### L'enzyme de restriction utilisée pour ces données est sbf1. Pour le savoir, nous avons regardé le pourcentage de "ACTG" dans l'onglet "per base sequence content". En effet la séquence suivante "TGCA GG" correspond à sbf1.

### 1.3) What is the 4 nt sequence preceeding the enzyme overhang?
#### La séquence qui précède l'enzyme de restriction est : TGCA

# Part 3 — Demultiplexing using barcodes

## 1) Using simple grep and sed, create 16 new FASTQ files, one for each sample, based on Details_Barcode_Population_SRR034310. Name the demultiplexed files as, for ex, BearPaw1.fastq (for barcode CCCC). 

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

## 2)  Nous comptons le nombre de lecture de chaque fichier compter le nombre de reads 
grep "^>SRR" BearPaw1.fastq | wc -l # 15802 lectures
grep "^>SRR" BearPaw2.fastq | wc -l # 15754 lectures
grep "^>SRR" BearPaw3.fastq | wc -l # 15314 lectures
grep "^>SRR" BearPaw4.fastq | wc -l # 23119 lectures
grep "^>SRR" BearPaw5.fastq | wc -l # 24149 lectures
grep "^>SRR" BearPaw6.fastq | wc -l # 67266 lectures
grep "^>SRR" BearPaw7.fastq | wc -l # 44927 lectures
grep "^>SRR" BearPaw8.fastq | wc -l # 68112 lectures
grep "^>SRR" RabbitSlough1.fastq | wc -l # 37965 lectures
grep "^>SRR" RabbitSlough2.fastq | wc -l # 92388 lectures
grep "^>SRR" RabbitSlough3.fastq | wc -l # 34596 lectures
grep "^>SRR" RabbitSlough4.fastq | wc -l # 34087 lectures
grep "^>SRR" RabbitSlough5.fastq | wc -l # 36019 lectures
grep "^>SRR" RabbitSlough6.fastq | wc -l # 64961 lectures
grep "^>SRR" RabbitSlough7.fastq | wc -l # 98983 lectures
grep "^>SRR" RabbitSlough8.fastq | wc -l # 55038 lectures

## 3) Nous enlevons les 4 premiers nucléotides qui correpsondent aux barcodes : 
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

### Vérification d'un des fichiers pour svaoir si tout fonctionne 
RabbitSlough8_trim.fatq | head

# Part 4 — Mapping to a reference genome
## 1) Index the reference
bwa-mem2 index Reference_genome_chrI.fasta

## 2) Map each demultiplexed FASTQ
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

## 3) Convert to BAM, sort, index
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

### Nous mettons tout dans le même fichier  
scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/*.bam' ~ /home/2025LBISM2/e22402344/

## 4) Compute mapping statistics
### 4.1) Mapping stats summarized: % mapped, number of reads, number of duplicates (if info available)
#### Le premier résultat est le pourcentage, et le second est le nombre de lecture 
samtools flagstat BearPaw1_sorted.bam # 15802 & 1092
samtools flagstat BearPaw2_sorted.bam # 15554 & 1144
samtools flagstat BearPaw3_sorted.bam # 15314 & 1192
samtools flagstat BearPaw4_sorted.bam # 23119 & 1685
samtools flagstat BearPaw5_sorted.bam # 24149 & 1769
samtools flagstat BearPaw6_sorted.bam # 67266 & 5413
samtools flagstat BearPaw7_sorted.bam # 44927 & 3389
samtools flagstat BearPaw8_sorted.bam # 68112 & 5576

samtools flagstat RabbitSlough1_sorted.bam # 37965 & 2873
samtools flagstat RabbitSlough2_sorted.bam # 92388 & 6791
samtools flagstat RabbitSlough3_sorted.bam # 34596 & 2588
samtools flagstat RabbitSlough4_sorted.bam # 34087 & 2440
samtools flagstat RabbitSlough5_sorted.bam # 136019 & 9453
samtools flagstat RabbitSlough6_sorted.bam # 64961 & 4862
samtools flagstat RabbitSlough7_sorted.bam # 98983 & 7264
samtools flagstat RabbitSlough8_sorted.bam # 5538 & 4100

#### Le mapping est le pourcentage d'alignement, correspond à la proportion de lectures (reads) d'un fichier FASTAQ qui ont été correctement alignées sur un génome de référence. EN pratique, avec samtools flagstat, nous obtenons 2 nombre principaux. LE premie rest le nombre de lectures totales dans el BAM, et le second est le nombre de lectures correctement mappées. Le mapping se calcul par = read mappées / reads totales * 100. 
#### Les génomesmarins ont souvent un mapping plus faible pour plusieurs raisons biologiques et techniques. En effet, la diversité génomique est élevvé. Les populations marines ont souvent des populations très polymorphes et les génomes de références ne couvrent pas toutes les vaiantes, donc moins de lectures vont s'aligner. De ^lus, les répétitions sont abondantes. Les séquences sont divergentes, si la référnce utilisée n'est pas exactement de la même population ou espèce, les divergences gééntiques font baisser le mapping. Enfin, la qualité des lectures et la contamination peuvent influencer de faible résultat de mapping (provenant de virus/bactérie marins). 
   

# Part 5 — Calling SNPs
## 1) Index your reference
samtools faidx Reference_genome_chrI.fasta 

## 2) Create a vcf folder and enter it. 
mkdir vcf
cd vcf

## 3) Call variants from the two BAM files
bcftools mpileup -Ou -f ../Reference_genome_chrI.fasta ../BearPaw1_sorted.bam ../BearPaw2_sorted.bam ../BearPaw3_sorted.bam ../BearPaw4_sorted.bam ../BearPaw5_sorted.bam ../BearPaw6_sorted.bam ../BearPaw7_sorted.bam ../BearPaw8_sorted.bam ../RabbitSlough1_sorted.bam ../RabbitSlough2_sorted.bam ../RabbitSlough3_sorted.bam ../RabbitSlough4_sorted.bam ../RabbitSlough5_sorted.bam ../RabbitSlough6_sorted.bam ../RabbitSlough7_sorted.bam ../RabbitSlough8_sorted.bam | bcftools call -mv -Ov -o raw_variants.vcf

vcftools --vcf raw_variants.vcf --freq 
vcftools --vcf raw_variants.vcf --freq
## 3.1) How many SNPs did you call ? 
#### Après cette première étape, il y a 127 SNPs

## 4)  Using vfctools, filter SNPs, using : vcftools --vcf raw_variants.vcf --minDP 5 --max-missing 1 --min-alleles 2 --max-alleles 2 --recode --out filtered. Explain what this command is doing. How many SNPs remain ?
vcftools --vcf raw_variants.vcf --minDP 5 --max-missing 1 --min-alleles 2 --max-alleles 2 --recode --out filtered 
#### Après cette étape de filtrage, il ne reste plus que 4 SNPs. Cette commande présente le "mindp" = 5, qui est la profondeur de lecture de au moins 5. PUis, le "max-missing" de 100% correspond au SNP gardés si ils sont génotypés chez tous les indidus. Enfin, le "min-allèle" et le "max-allèle" corespondent aux minimum au maximum d'allèles utilisées qui sont de 2. 

## 5) Using vfctools, compute the allelic frequence and the FST per sites
vcftools --vcf raw_variants.vcf --freq  --out allele_freqs
vcftools --vcf raw_variants.vcf --weir-fst-pop popbear.txt --weir-fst-pop poprabbit.txt --out fst_pop

##### Afin de télélcharger les données sur nos ordinateur de la salle, nous avons utilisées les commande suivante : 
scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/vcf/*.frq' ~ /home/2025LBISM2/e22402344/
scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/vcf/*.vcf' ~ /home/2025LBISM2/e22402344/

## 6) Visualize in R allele frequencies and FST values
### Les résultats des graphiques se truvent dans les fichiers joins rendu avec le reste de l'examen. 


# Partie 6 - Concept interpretation
## 1) What is the difference between coverage and depth of coverage?
### Le covergae est le ratio du nombre totales de bases obtenus sur la taille du génome entier. Donc combien de fois, en moyenne, chaque base du génome a été lue. La profondeur de séquencage correspond au pourcentage du génome cible qui a été séquencé au moins une fois. Une couverture plus élevée signifie une plus grande fiabilité. La profondeur et la couverture sont positivement corrélées 

## 2) Why do RADseq datasets contain many loci with missing data?
### En RADseq, on réduit le génome pour par séquencer le génome entier mais de manière alétaoire aléatoire.  On utilise les enzymes de restriction qui digèrent une partie spécifique. Le but est de séquencer de part et d’autres de l’enzyme donc seulement les fragments d'ADN qui sont autour des sites de coupes vont etre séquencés. On a aussi une divergence importante du coverage entre les différenets endroits.

## 3) Why is it important to apply SNP filtering before population genomic analyses?
La filtration garantit que les conclusions biologiques finales sont basées sur de réelles différences génétiques, et non sur des artefacts techniques du processus de séquençage.
Ces erreurs peuvent  etre du à la machine de séquençage en elle même. Cela peut aussi etre du à des erreurs d'alignement. On peut aussi supprimer les sites qui n'ont pas une couverture de séquençage suffisante pour être appelés avec précision.

Peut aussi enlever les allèles qui sont très rares car permet de réduire le bruit et c'est souvent des allèles peut fiable. On pourrait par la suite confondre le bruit technique aux réeeles signatures biologiques.

## 4) What is an outlier locus? Give a definition from CM1.
Un ilot de différenciation se forme quand une région de génome est sous sélection.

L'espèce Dicentrarchus labrax présente certaines régions sous ilots de différenciation entre la lignée atlantique et la lignée méditerranéenne du bar européen autour de 10 000 000 pb. Ces îlots de différenciation ont été générés par des taux d'introgression variables (flux génique après le contact secondaire) à travers le génome, suite à une période d'isolement géographique - Tine et al 2014

0638484675
    Give an example of a marine organism where genomic scans detected islands of differentiation.
SALMO SALSA/ TRUTTA
