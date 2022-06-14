```
# Files in Google Drive, downloaded as zip and uploaded to dev via scp
# Some files needed to be unzipped
unzip UCSC.ADT-CITE-seq.csv.zip 
unzip UCSC.metadata.csv.zip 

# Convert meta from csv ->tsv
cat orig/UCSC.metadata.csv |tr "," "\t" > UCSC.metadata.tsv
# combine orignal meta file with new one
cbTool metaCat meta.tsv UCSC.metadata.tsv meta.combined.tsv

# Made a quick genes file for  RNA-seq dataset, but cols were in wrong order
awk 'BEGIN {FS=","; OFS=","} {print $2,$1}' fetal-liver-markers.badOrder.csv > fetal-liver-markers.csv

# Split dataset into scRNA-seq and cite-seq datasets

# markers genes were in xlsx format, exported to tsv
xlsx2csv -d tab UCSC.DEG.clusterMarkers.xlsx ../markers.tsv

# cbBuild didn't like order of columns, was close to seurat format, but not close enough
# just pust cluster/gene columns first
awk 'BEGIN {FS="\t"; OFS="\t"} {print $6,$7,$1,$2,$3,$4,$5}' markers.tsv > markers.orderFixed.tsv

# Wrote script to transpose cite-seq matrix
./matrix_transpose UCSC.ADT-CITE-seq.csv
mv UCSC.ADT-CITE-seq.csv.transposed UCSC.ADT-CITE-seq.tsv

# made quick genes file out all genes in cite-seq matrix
cut -f1 UCSC.ADT-CITE-seq.tsv |grep -v cellID > geneList.tsv
```
