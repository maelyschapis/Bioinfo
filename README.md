EXAMEN BIOINFORMATIQUE MAËLYS CHAPIS & MILA TRIPON

Ce dépôt contient l’ensemble du travail réalisé pour l’examen de bioinformatique, incluant la préparation des fichiers, le contrôle qualité des données et l’analyse des séquences.

# Vérification du directory et création dossier finalexam
```
pwd
```
Cette commande permet de s’assurer que nous sommes bien dans le répertoire $HOME de l’utilisateur, ce qui est important pour organiser correctement nos fichiers.

# Part 1 — Linux basics 
## 1) Create a working directory called finalexam on your student account, in your $HOME directory. Copy the dataset from filesender link above.
Ensuite, nous créons un dossier finalexam pour stocker toutes les données de l’examen :
```
kdir finalexam
```
Nous copions ensuite les fichiers nécessaires depuis le serveur filesender, en indiquant dans un premier lieu le fichier puis l'adresse d'arrivée souhaitée :
```
scp SRR034310_10pc.fastq /home/2025LBISM2/e22402344/finalexam
scp Details_Barcode_Population_SRR034310.txt /home/2025LBISM2/e22402344/finalexam
scp Reference_genome_chrI.fasta /home/2025LBISM2/e22402344/finalexam
```
Cette étape permet de centraliser toutes les données dans un seul dossier pour faciliter les analyses ultérieures.

## 2) Explore file content with bash commands using and report: 
Pour comprendre la structure des fichiers, nous utilisons des commandes comme cat et head :
```
cat SRR034310_10pc.fastq | head # pour voir à quoi ressemble le fichier
```
Cette commande affiche les premières lignes du fichier FASTQ pour vérifier le format et le type de données contenues.

### 2.1) How many lines are in the fastq file?
```
wc -l < SRR034310_10pc.fastq 
```
Résultat : 356412 lignes
Comme chaque séquence FASTQ occupe 4 lignes (identifiant, séquence, ligne intermédiaire, qualité), on va donc diviser ce nombre par 4 pour avoir le nombre total de séquences.
```
nseq=$((`wc -l < SRR034310_10pc.fastq / 4))
e22402344@ed55:~$ echo $nseq
```
Résultat : 89703 séquences
Cela montre que le fichier contient presque 90 000 lectures, ce qui est suffisant pour effectuer des analyses statistiques fiables.


### 2.2) What is the read length?

```
cat SRR034310_10pc.fastq | head
```
Chacune des séquences est composées de 36 nucloétides. 

### 2.3) How many barcodes are in Details_Barcode_Population_SRR034310?
```
wc -l < Details_Barcode_Population_SRR034310.txt
```
Résultat : 16 barcodes
Chaque barcode correspond à un individu ou à un échantillon différent, ce qui permettra d’identifier l’origine des séquences.

# Part 2 — Quality control 
```
home/2025LBISM2/e22402344/fastqc_v0.12.1/FastQC/./fastqc $1
```
Cette commande permet d'ouvrir le logiciel Fastqc en dehors du terminal. Dans celui-ci nous ouvrons notre fichier de séquences puis génèrons un rapport HTML détaillé sur la qualité des lectures, qui peut être visualisé pour identifier des problèmes éventuels.

### 1.1) Describe : sequence length distribution, quality drop at 5' and 3' ends of reads (if any), presence of adapters and overrepresented sequences

- Sequence length distribution : La longueur des séquences est uniforme, avec une valeur de 36 pb.
  
<img width="1156" height="835" alt="sequence length distribution" src="https://github.com/user-attachments/assets/526f011d-c22e-4b26-a198-1c8630ec59b2" />

- Quality drop at 5' and 3' ends : Le graphique Per base sequence quality montre la qualité moyenne à chaque position des lectures (de la base 1 à 36).La qualité des séquences est globalement bonne entre 11 et 22 pb, mais on observe une baisse aux extrémités. En général, un score Phred au-dessus de 28 (zone verte) est bon. Si la courbe tombe dans l'orange ou le rouge, cela signifie que la machine de séquençage a fait beaucoup d'erreurs en lisant ces bases. Sur des lectures aussi courtes (36bp), une mauvaise qualité peut rendre les données difficiles à utiliser.
  
<img width="1108" height="837" alt="per basee sequence quality" src="https://github.com/user-attachments/assets/3a3dd002-fcb5-41f2-8d66-c9b34b118b92" />

- Présence d’adaptateurs : Aucun adaptateur détecté.
  
<img width="1132" height="817" alt="adapter content" src="https://github.com/user-attachments/assets/e04bf229-a92c-47da-be2e-1b558daeae47" />

- Overrepresented sequences : Trois séquences sont sur-représentées dans le fichier, ce qui peut indiquer des biais techniques (amplification PCR excessive) ou biologiques (certains gènes sont très exprimés).
  
<img width="878" height="237" alt="overrepresented sequence" src="https://github.com/user-attachments/assets/4e1c4167-f0c4-47a8-b2a6-e5230dfa514d" />

Rapport complet : file:///home/2025LBISM2/e22402344/qc/SRR034310_10pc_fastqc.html

### 1.2) which restriction enzyme was used to create these data?
L’enzyme utilisée pour générer ces données est SbfI.
Cette information est déduite à partir du pourcentage de chaque nucléotides dans l’onglet "per base sequence content" : la séquence TGCA GG correspond au site de coupure de SbfI.

<img width="1117" height="821" alt="per base sequence content" src="https://github.com/user-attachments/assets/b18a2203-f1a1-4c7e-92f5-9357cc67fb4b" />


### 1.3) What is the 4 nt sequence preceeding the enzyme overhang?
La séquence précédant le site de l’enzyme de restriction est un barcode, permettant de reconnaitre notre échantillon. 

# Part 3 — Demultiplexing using barcodes

## 1) Using simple grep and sed, create 16 new FASTQ files, one for each sample, based on Details_Barcode_Population_SRR034310. Name the demultiplexed files as, for ex, BearPaw1.fastq (for barcode CCCC). 
Pour créer un fichier FASTQ pour chaque échantillon, nous avons utilisé les commandes grep et sed :
```
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
```
Explications :
- grep "^BARCODE" permet de sélectionner les séquences commençant par un barcode spécifique.
- B1 -A2 inclut une ligne avant et deux lignes après le match, correspondant aux quatre lignes de chaque lecture FASTQ (identifiant, séquence, ligne intermédiaire, qualité).
- sed 's/@SRR/@SRR/g' sert ici à corriger ou standardiser l’entête des séquences.
- Chaque fichier créé correspond à un échantillon identifié par son barcode, par exemple BearPaw1.fastq pour le barcode CCCC.

## 2)  Nous comptons le nombre de lecture de chaque fichier 
Pour vérifier que le démultiplexage a fonctionné correctement, nous comptons le nombre de lectures de chaque fichier FASTQ :
```
grep "^@SRR" BearPaw1.fastq | wc -l # 15802 lectures
grep "^@SRR" BearPaw2.fastq | wc -l # 15754 lectures
grep "^@SRR" BearPaw3.fastq | wc -l # 15314 lectures
grep "^@SRR" BearPaw4.fastq | wc -l # 23119 lectures
grep "^@SRR" BearPaw5.fastq | wc -l # 24149 lectures
grep "^@SRR" BearPaw6.fastq | wc -l # 67266 lectures
grep "^@SRR" BearPaw7.fastq | wc -l # 44927 lectures
grep "^@SRR" BearPaw8.fastq | wc -l # 68112 lectures
grep "^@SRR" RabbitSlough1.fastq | wc -l # 37965 lectures
grep "^@SRR" RabbitSlough2.fastq | wc -l # 92388 lectures
grep "^@SRR" RabbitSlough3.fastq | wc -l # 34596 lectures
grep "^@SRR" RabbitSlough4.fastq | wc -l # 34087 lectures
grep "^@SRR" RabbitSlough5.fastq | wc -l # 36019 lectures
grep "^@SRR" RabbitSlough6.fastq | wc -l # 64961 lectures
grep "^@SRR" RabbitSlough7.fastq | wc -l # 98983 lectures
grep "^@SRR" RabbitSlough8.fastq | wc -l # 55038 lectures
```
Explication :
- grep "^>SRR" recherche les lignes d’identifiant de chaque séquence.
- wc -l compte le nombre de lignes correspondantes, soit le nombre total de lectures dans le fichier.
- Cela permet de s’assurer que toutes les séquences ont été correctement assignées à chaque échantillon.

## 3) Nous enlevons les 4 premiers nucléotides qui correspondent aux barcodes : 
Les quatre premiers nucléotides de chaque séquence correspondent au barcode. Nous les retirons avec seqtk pour préparer les séquences pour les analyses ultérieures :
```
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
```
Explication :
- L’option -b 4 supprime les 4 premiers nucléotides de chaque lecture.
- Les fichiers résultants (*_trim.fastq) contiennent les séquences sans barcode, prêtes pour un alignement ou un contrôle qualité.

Pour vérifier que la suppression des barcodes a bien fonctionné :
```
RabbitSlough8_trim.fatq | head
```
Cette commande affiche les premières lignes du fichier trimé, permettant de confirmer que les barcodes ont été retirés et que le format FASTQ est correct.

# Part 4 — Mapping to a reference genome
## 1) Index the reference
Cette section décrit l’alignement des séquences sur le génome de référence, suivi de la conversion et de l’indexation des fichiers pour analyses ultérieures.
Avant d’aligner les séquences, il est nécessaire de créer un index du génome de référence :
```
bwa-mem2 index Reference_genome_chrI.fasta
```
La commande bwa-mem2 index prépare le fichier FASTA pour l’alignement. L’index permet à l’algorithme BWA de retrouver rapidement les positions correspondantes sur le génome.

## 2) Map each demultiplexed FASTQ
Chaque fichier FASTQ trimé est aligné sur le génome de référence :
```
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
```
La commande "bwa-mem2 mem" effectue l’alignement des séquences courtes sur le génome de référence. Le résultat est un fichier SAM (Sequence Alignment/Map) qui contient toutes les positions de correspondance, la qualité de l’alignement, et d’autres informations essentielles.

## 3) Convert to BAM, sort, index
Les fichiers SAM sont volumineux et non optimisés pour les analyses. Nous les convertissons en BAM, un format compressé et trié, puis nous les indexons.
```
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
```
La commande "samtools view -bS" permet de convertire le SAM en BAM. "Samtools sort" permet quant à lui de trier les lectures par position sur le génome pour faciliter les analyses et la visualisation. "Samtools index" crée un fichier index (.bai) qui permet d’accéder rapidement à des positions spécifiques dans le BAM.

Une fois tous les fichiers alignés, triés et indexés, nous les rassemblons dans notre répertoire personnel pour analyses ultérieures :
 ```
scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/*.bam' ~ /home/2025LBISM2/e22402344/
```

## 4) Compute mapping statistics
Après avoir aligné les séquences sur le génome de référence, il est important d’évaluer la qualité et l’efficacité de l’alignement. Pour cela, nous utilisons samtools flagstat.
### 4.1) Mapping stats summarized: % mapped, number of reads, number of duplicates (if info available)
```
samtools flagstat BearPaw1_sorted.bam # 15802 & 1092 (6,91%)
samtools flagstat BearPaw2_sorted.bam # 15554 & 1144 (7,26%)
samtools flagstat BearPaw3_sorted.bam # 15314 & 1192 (7,78%)
samtools flagstat BearPaw4_sorted.bam # 23119 & 1685 (7,29%)
samtools flagstat BearPaw5_sorted.bam # 24149 & 1769 (7,33%)
samtools flagstat BearPaw6_sorted.bam # 67266 & 5413 (8,05%)
samtools flagstat BearPaw7_sorted.bam # 44927 & 3389 (7,54%)
samtools flagstat BearPaw8_sorted.bam # 68112 & 5576 (8,19%)

samtools flagstat RabbitSlough1_sorted.bam # 37965 & 2873 (7,57%)
samtools flagstat RabbitSlough2_sorted.bam # 92388 & 6791 (7,35%)
samtools flagstat RabbitSlough3_sorted.bam # 34596 & 2588 (7,48%)
samtools flagstat RabbitSlough4_sorted.bam # 34087 & 2440 (7,16%)
samtools flagstat RabbitSlough5_sorted.bam # 136019 & 9453 (6,95%)
samtools flagstat RabbitSlough6_sorted.bam # 64961 & 4862 (7,48%)
samtools flagstat RabbitSlough7_sorted.bam # 98983 & 7264 (7,34%)
samtools flagstat RabbitSlough8_sorted.bam # 5538 & 4100 (7,45%)
```

Avec Samtools, nous obtenons deux nombres principaux :
- Nombre total de lectures qui ont passés le controle qualité. 
- Nombre de lectures correctement mappées (celles qui se sont alignées de façon fiable sur le génome de référence).

Aucun duplicat n'est détecté

Les "samtools flagstat" fournissent un résumé complet des alignements dans le fichier BAM. Les principaux indicateurs sont :
- % mapped : pourcentage de lectures alignées correctement sur le génome de référence. Il se calcule ainsi : Mapping (%) = (lectures mappées / lectures totales)×100
- Number of reads : nombre total de lectures dans le fichier BAM.
- Number of duplicates : nombre de lectures dupliquées (lorsque l’information est disponible), souvent liées à la PCR ou à la préparation de la librairie.

### 4.2)  Why do marine genomes often produce lower mapping %
Chez les génomes marins, le pourcentage de mapping est souvent plus faible que pour d’autres organismes, pour plusieurs raisons biologiques et techniques :
- Diversité génomique élevée :
Les populations marines présentent un polymorphisme génétique important. Le génome de référence ne représente souvent qu’une seule population ou un seul individu, donc de nombreuses variantes présentes dans les échantillons ne s’aligneront pas correctement.
- Répétitions abondantes :
Les génomes marins contiennent de nombreuses séquences répétées ou transposons. Les lectures issues de ces régions peuvent s’aligner de manière ambiguë, ce qui fait baisser le mapping effectif.
- Divergence génétique entre populations ou espèces :
Si la référence utilisée n’est pas exactement de la même population ou espèce que l’échantillon, les différences nucléotidiques (mutations, insertions, délétions) réduisent le nombre de lectures alignées.
- Qualité des lectures et contamination :
Les lectures de mauvaise qualité peuvent échouer à s’aligner. De plus, dans les échantillons marins, il est courant de trouver des contaminations par des virus, bactéries ou autres organismes marins, ce qui génère des séquences qui ne correspondent pas au génome cible.

Le mapping est donc un indicateur clé de la qualité des données et de la compatibilité entre les lectures et le génome de référence. Les faibles taux de mapping dans les génomes marins sont le reflet d’une combinaison de diversité génétique, de répétitions et de facteurs techniques liés à l’échantillonnage et à la qualité des séquences.

Lorsqu’on analyse les fichiers BAM après mapping, samtools flagstat fournit parfois le nombre de lectures dupliquées. Les lectures dupliquées sont des séquences identiques présentes plusieurs fois dans le fichier, et elles peuvent apparaître pour plusieurs raisons :
- Amplification PCR :
Lors de la préparation de la librairie, certaines fragments d’ADN sont amplifiés plusieurs fois. Si une même séquence est amplifiée à plusieurs reprises, elle apparaît comme un duplicate.
- Répétitions génomiques :
Dans les régions répétées du génome, il est possible que des lectures provenant de différentes copies se retrouvent identiques, donnant l’impression de duplications.
- Séquences biologiquement réelles :
Parfois, plusieurs copies identiques peuvent exister naturellement dans l’échantillon, surtout dans les génomes polyploïdes ou fortement répétitifs.

Impact des duplications sur l’interprétation des données
- Surestimation de la couverture : Les duplications peuvent donner l’impression qu’une région du génome est surreprésentée, alors qu’il s’agit de copies artificielles générées par la PCR.
- Biais dans les analyses quantitatives : Si l’on mesure l’abondance de séquences ou l’expression génétique, les duplications peuvent fausser les résultats.
- Filtres nécessaires : Dans de nombreux pipelines bioinformatiques, on supprime ou marque les duplications avant d’effectuer des analyses de variant calling, de couverture ou de diversité génétique.

Remarque pour les génomes marins : Les faibles taux de mapping combinés à des duplications modérées sont fréquents. La diversité génétique et la présence de séquences répétées accentuent ce phénomène, et il est important de les considérer pour éviter des biais dans l’interprétation des données.

C'est le cas par exemple chez *Crassostrea gigas*. Zhang G et al (2012) ont pu voir que le génome de l'huitre est très polymorphe. On peut détecter 1 SNP toutes les 60 à 80 paires de bases. Par conséquence, un logiciel de mapping classique, peut avoir tendance a rejeter la diversité (autorise que quelques erreurs par lecture) et faire chuter le taux de mapping. Environ 36% du génome de *C.gigas* est composé d'éléments répétés. Ces séquences peuvent s'alligner à plusieurs endroits différents et etre encore une fois rejetées, réduisant la confiance dans l'alignement.

Zhang, G., et al. (2012). "The oyster genome reveals stress adaptation and complexity of shell formation." Nature, 490(7418), 49-54.

# Part 5 — Calling SNPs
Cette partie décrit la détection de SNPs (Single Nucleotide Polymorphisms) à partir des fichiers BAM alignés sur le génome de référence.

## 1) Index your reference
Avant de réaliser le variant calling, nous indexons le fichier FASTA :
```
samtools faidx Reference_genome_chrI.fasta
```

## 2) Create a vcf folder and enter it. 
Nous centralisons tous les fichiers VCF dans un dossier dédié :
```
mkdir vcf
cd vcf
```

## 3) Call variants from the BAM files
```
bcftools mpileup -Ou -f ../Reference_genome_chrI.fasta ../BearPaw1_sorted.bam ../BearPaw2_sorted.bam ../BearPaw3_sorted.bam ../BearPaw4_sorted.bam ../BearPaw5_sorted.bam ../BearPaw6_sorted.bam ../BearPaw7_sorted.bam ../BearPaw8_sorted.bam ../RabbitSlough1_sorted.bam ../RabbitSlough2_sorted.bam ../RabbitSlough3_sorted.bam ../RabbitSlough4_sorted.bam ../RabbitSlough5_sorted.bam ../RabbitSlough6_sorted.bam ../RabbitSlough7_sorted.bam ../RabbitSlough8_sorted.bam | bcftools call -mv -Ov -o raw_variants.vcf

vcftools --vcf raw_variants.vcf --freq 
vcftools --vcf raw_variants.vcf --freq
```
La commande "bcftools mpileup" génère un "pileup" de toutes les lectures alignées, calculant la couverture et les bases présentes à chaque position du génome. L’option -f indique le fichier de référence. "bcftools call -mv" appelle les variants :
- m pour le modèle multi-allelic.
- v pour ne sortir que les positions variant (SNPs et indels).
- Ov indique que le fichier de sortie est au format VCF classique.

## 3.1) How many SNPs did you call ? 
Après cette étape, nous obtenons 127 SNPs dans le fichier raw_variants.vcf.

## 4)  Using vfctools, filter SNPs, using : vcftools --vcf raw_variants.vcf --minDP 5 --max-missing 1 --min-alleles 2 --max-alleles 2 --recode --out filtered. Explain what this command is doing. How many SNPs remain ?
```
vcftools --vcf raw_variants.vcf --minDP 5 --max-missing 1 --min-alleles 2 --max-alleles 2 --recode --out filtered
```
Concernant les comamndes utilisées : 
- minDP 5 : conserve seulement les SNPs dont la profondeur de lecture est d’au moins 5, pour s’assurer que la variation est bien supportée par plusieurs lectures.
- max-missing 1 : conserve uniquement les SNPs présents chez tous les individus (100% des individus génotypés).
- min-alleles 2 et --max-alleles 2 : conserve uniquement les SNPs bi-alloéliques, c’est-à-dire avec exactement deux variantes détectées.
- recode --out filtered : génère un nouveau fichier VCF filtré.

Après ce filtrage strict, il ne reste que 4 SNPs fiables.

## 5) Using vfctools, compute the allelic frequence and the FST per sites
```
vcftools --vcf raw_variants.vcf --freq  --out allele_freqs
vcftools --vcf raw_variants.vcf --weir-fst-pop popbear.txt --weir-fst-pop poprabbit.txt --out fst_pop
```
Cette commande calcule la fréquence de chaque allèle pour tous les SNPs dans chaque échantillon ou population. Les résultats sont enregistrés dans un fichier .frq, utile pour les analyses de diversité génétique. 
- weir-fst-pop permet de calculer le FST (indice de différenciation génétique) entre deux populations :
- popbear.txt : liste des individus de la population BearPaw.
- poprabbit.txt : liste des individus de la population RabbitSlough.
- Le fichier de sortie contient le FST pour chaque SNP, indiquant le degré de divergence génétique entre populations.

Afin de télélcharger les données sur nos ordinateur de la salle, nous avons utilisées les commandes suivantes : 
```
scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/vcf/*.frq' ~ /home/2025LBISM2/e22402344/
scp 'tp183376@core.cluster.france-bioinformatique.fr:/shared/home/tp183376/vcf/*.vcf' ~ /home/2025LBISM2/e22402344/
```

## 6) Visualize in R allele frequencies and FST values
### Les résultats des graphiques se trouvent dans les fichiers joins rendu avec le reste de l'examen. 

#### Histogramme pour les fréquences alléliques

Le graphique est un barplot des fréquences alléliques observées par position génomique, après avoir restructuré le fichier .frq. Il y a pour chaque SNP (CHROM + POS), plusieurs allèles possibles, chacun avec une fréquence. La fréquence de un des allèles est représentés. 


![Graphique de représentation des FST](https://github.com/maelyschapis/Bioinfo/blob/main/R/Frequence.svg)

La distribution des fréquences alléliques montre que la majorité des SNPs présents dans l’échantillon sont monomorphes, et ne présentent aucune variation. 12 SNPs ont une fréquence allélique égale à 1, ce qui indique que l’allèle alternatif est fixé et qu’aucune variation n’est observée à ces positions.

À l’inverse, seuls quatre SNPs sont polymorphes : un SNP présente une fréquence de 0,25, deux une fréquence de 0,5 et un une fréquence de 0,75. On a donc la présence de variation génétique ponctuelle, mais qui reste minoritaire dans l’ensemble du jeu de données.

Ces fréquences correspondent à différents niveaux de polymorphisme :
f = 0.25 → l’allèle majoritaire représente 75 % des chromosomes observés
f = 0.5 → les deux allèles sont présents en proportions égales (SNP biallélique parfaitement équilibré)
f = 0.75 → un allèle majoritaire domine, mais un second allèle est encore présent

La présence de ces quelques SNPs polymorphes montre que l’échantillon contient un peu de diversité génétique, mais la forte proportion de SNPs à fréquence = 1 indique une variabilité globale faible.


#### Histogramme pour les FST
Cette figure représente la différenciation génétique (FST) le long d’un génome, position par position, telle que calculée avec la méthode de Weir & Cockerham.

![Graphique de représentation des FST](https://raw.githubusercontent.com/maelyschapis/Bioinfo/6337f416578fe0da48e2269b49fb38f76b4c43c6/R/FST.svg)

Concernant l’axe X, c'est la position qui correspond aux coordonnées génomiques (sur un contig ou un chromosome). Chaque point est un SNP (ou une fenêtre, selon le fichier utilisé). Concernant l’axe Y, c'est le degré de différenciation génétique entre deux populations. 
De manière plus générale : 
- FST ≈ 0 → pas de différenciation
- FST > 0 → différenciation présente (plus la valeur est élevée, plus elle est forte)
- FST < 0 → valeurs négatives possibles avec Weir & Cockerham (généralement dues au bruit statistique). Elles ne sont pas biologiquement interprétables ; elles indiquent simplement "FST ≈ 0".

Ici, la distribution des point montre une variabilité du FST le long du génome :
- La majorité des points sont entre –0.2 et 0.4, ce qui suggère une différenciation faible à modérée entre les populations.
- Quelques points montent vers 0.6 – 0.9, potentiels sujets à sélection divergente ou zones très différenciées.
- De nombreux points faibles ou négatifs → régions neutres ou peu polymorphes.

Il est à noter qu'entre 8–12 Mb, on voit un petit “pic” de FST. Cela pourrait être dû à une possible région génomique où les populations diffèrent davantage. Une valeur très haute autour de 20 Mb pourrait quant à elle correspondre :
- à un locus sous pression de sélection,
- à un artefact (faible couverture, faible fréquence allélique…).

Finalement, cette figure donne une vue exploratoire de la différenciation génétique des populations le long du génome :
- Points hauts = différenciation forte, donc candidats à la sélection ou isolation génétique.
- Points bas/négatifs = pas de différenciation.
- La distribution irrégulière suggère une différenciation hétérogène à travers le génome.

# Partie 6 - Concept interpretation
## 1) What is the difference between coverage and depth of coverage?
Le covergae est le pourcentage du génome cible qui a été séquencé au moins une fois. La profondeur de séquencage est le ratio du nombre totales de bases obtenus sur la taille du génome entier. Donc combien de fois, en moyenne, chaque base du génome a été lue. Par exemple, une profondeur de "30x" signifie que chaque base a été lue en moyenne 30 fois. Une couverture plus élevée signifie une plus grande fiabilité. 

## 2) Why do RADseq datasets contain many loci with missing data?
En RADseq, on réduit la complexité du génome en utilisant des enzymes de restriction qui clivent l’ADN à des sites spécifiques. Le but est de séquencer de part et d’autres de l’enzyme donc seulement les fragments d'ADN qui sont autour des sites de coupes. De plus, la couverture n’est pas uniforme : certains loci sont séquencés plus profondément que d’autres. Comme le séquençage est un processus aléatoire, certains fragments peuvent ne pas être suffisamment séquencés, ce qui entraîne des données manquantes à certains loci dans l’ensemble du dataset.

## 3) Why is it important to apply SNP filtering before population genomic analyses?
La filtration garantit que les conclusions biologiques finales sont basées sur de réelles différences génétiques, et non sur des artefacts techniques du processus de séquençage.
Ces erreurs peuvent  etre du à la machine de séquençage en elle même qui peut faire des erreurs de lecture des bases. Cela peut aussi etre du à des erreurs d'alignement. Des séquences issues de régions dupliquées du génome peuvent s'aligner au meme endroit par erreur, créant de faux hétérozygotes.  On peut aussi supprimer les sites qui n'ont pas une couverture de séquençage suffisante pour être appelés avec précision et les allèles qui sont très rares car cela permet de réduire le bruit et c'est souvent des allèles peut fiable. On pourrait par la suite confondre le bruit technique aux réelles signatures biologiques.

## 4) What is an outlier locus? Give a definition from CM1.

Un locus outlier c'est un marqueur génétique qui présente un niveau de différenciation statistiquement beaucoup plus élevé  que le reste du génome. Il diffère des attendues théoriques neutres. Un locus outlier est supposé etre sous l'influence de la sélection naturelle. 

## 5) Give an example of a marine organism where genomic scans detected islands of differentiation.

Un ilot de différenciation se forme quand une région de génome est sous sélection.

L'espèce *Dicentrarchus labrax* présente certaines régions sous ilots de différenciation entre la lignée atlantique et la lignée méditerranéenne du bar européen autour de 10 000 000 pb. Ces îlots résultent d'un contact secondaire après une période d'isolement géographique. Lors de ce contact, les flux de gènes ont repris, homogénéisant la majeure partie du génome. Cependant, les régions contenant des gènes impliqués dans l'adaptation locale ou l'isolement reproducteur ont résisté à ce flux de gènes (sélection contre les hybrides), créant ces îlots de haute différenciation.

Tine, M., Kuhl, H., Gagnaire, PA. et al. European sea bass genome and its variation provide insights into adaptation to euryhalinity and speciation. Nat Commun 5, 5770 (2014). https://doi.org/10.1038/ncomms6770

