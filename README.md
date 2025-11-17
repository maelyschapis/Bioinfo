EXAMEN BIOINFORMATIQUE MAÊLYS CHAPIS & MILA TRIPON
# Vérification du directory
pwd

e22402344@ed55:~$ mkdir finalexam
e22402344@ed55:~$ scp SRR034310_10pc.fastq /home/2025LBISM2/e22402344/finalexam
e22402344@ed55:~$ scp Details_Barcode_Population_SRR034310.txt /home/2025LBISM2/e22402344/finalexam
e22402344@ed55:~$ scp Reference_genome_chrI.fasta /home/2025LBISM2/e22402344/finalexam

22402344@ed55:~$ nseq=$((`wc -l < SRR034310_10pc.fastq / 4))
e22402344@ed55:~$ echo $nseq
897003

#16 barcodes
wc -l < Details_Barcode_Population_SRR034310.txt
16


###Partie 2
home/2025LBISM2/e22402344/fastqc_v0.12.1/FastQC/./fastqc $1

Describe in plain language:

    sequence length distribution
    # pic autour de 36 pb toutes noss séquences de la meme longuer
    
   # quality drop at 5' and 3' ends of reads (if any)
    # qualité bonne entre 11 et 22 pb 
    # oui on a un drop 
    
    presence of adapters
    #pas de présence d'adaptateurs
    
    overrepresented sequences
    # 3 séquences 

which restriction enzyme was used to create these data?
#sbf1, regarde par rapport au pourcentage de atcg dans per base sequence content 
TGCA GG qui correspond à  sbf1

what is the 4 nt sequence preceeding the enzyme overhang?
#TGCA

# Part 3 — Demultiplexing using barcodes

e22402344@ed55:~$ grep -B1 -A2 "^CCAA" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw2.fast
e22402344@ed55:~$ grep -B1 -A2 "^CCCC" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw1.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CCTT" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw3.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CCGG" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw4.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CACA" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw5.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CAAC" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw6.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CATG" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw7.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CAGT" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > BearPaw8.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CTCT" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough1.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CTAG" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough2.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CTTC" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough3.fast 
e22402344@ed55:~$ grep -B1 -A2 "^CTGA" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough4.fast 
e22402344@ed55:~$ grep -B1 -A2 "^GGGG" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough5.fast 
e22402344@ed55:~$ grep -B1 -A2 "^GGAA" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough6.fast 
e22402344@ed55:~$ grep -B1 -A2 "^GGTT" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough7.fast 
e22402344@ed55:~$ grep -B1 -A2 "^GGCC" SRR034310_10pc.fastq| sed 's/@SRR/>SRR/g' > RabbitSlough8.fast 


#compter le nombre
grep "^>SRR" BearPaw1.fast | wc -l
15802

15754

22402344@ed55:~$ grep "^>SRR" BearPaw4.fast | wc -l
23119
e22402344@ed55:~$ grep "^>SRR" BearPaw5.fast | wc -l
24149
e22402344@ed55:~$ grep "^>SRR" BearPaw6.fast | wc -l
67266
e22402344@ed55:~$ grep "^>SRR" BearPaw7.fast | wc -l
44927
e22402344@ed55:~$ grep "^>SRR" BearPaw8.fast | wc -l
68112
e22402344@ed55:~$ grep "^>SRR" RabbitSlough1.fast | wc -l
37965
e22402344@ed55:~$ grep "^>SRR" RabbitSlough2.fast | wc -l
92388
e22402344@ed55:~$ grep "^>SRR" RabbitSlough3.fast | wc -l
34596
e22402344@ed55:~$ grep "^>SRR" RabbitSlough4.fast | wc -l
34087
e22402344@ed55:~$ grep "^>SRR" RabbitSlough5.fast | wc -l
136019
e22402344@ed55:~$ grep "^>SRR" RabbitSlough6.fast | wc -l
64961
e22402344@ed55:~$ grep "^>SRR" RabbitSlough7.fast | wc -l
98983
e22402344@ed55:~$ grep "^>SRR" RabbitSlough8.fast | wc -l
55038

puis enlever les barcodes:
 seqtk trimfq -b 4 -e 10 BearPaw1.fast > BearPaw1.1.fast




    









