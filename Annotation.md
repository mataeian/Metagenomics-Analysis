# Metagenome Annotation

The annotation includes gene predication (tRNA, rRNA, CRISPR, signal peptide, transmembrane helices, and protein coding genes), taxonomic assignment, and gene function annotation. I used the first version of our in house tool called Metaerg [https://www.frontiersin.org/articles/10.3389/fgene.2019.00999/full](metaerg).

```
perl metaerg.pl --mincontiglen 500 --minorflen 180 --prefix Test --outdir Metaerg --locustag test --increment 1 --cpus 8 --evalue 1e-05 --identity 30 --coverage 70 -hmmcutoff --cut_tc contig.500.fasta
``` 

You can check your output file with the HTML result page which visually summerises your data.