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
    quality drop at 5' and 3' ends of reads (if any)
    presence of adapters
    overrepresented sequences









