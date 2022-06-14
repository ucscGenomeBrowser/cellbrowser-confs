```
# Download starting data
wget https://atlas.fredhutch.org/data/nygc/multimodal/pbmc_multimodal.h5seurat

## Within R ##
library(Seurat)
library(SeuratDisk)

hfile <- Connect("multimodal-pbmc.h5Seurat")
# Export data from h5seurat (requires SeuratDisk package in R):
#Transcriptomics matrix:
writeMM(as.sparse(hfile[["assays/SCT/data"]]), matrixPath)
#Genes/features for scRNA-seq:
write(as.matrix(hfile[["assays/SCT/features"]]), genesPath)
#Cell names (same for both ADT and scRNA-seq):
write(as.matrix(hfile[["cell.names"]]), barcodesPath)
#ADT matrix:
writeMM(as.sparse(hfile[["assays/ADT/counts"]]), "adt.matrix.mtx")
#ADT genes:
write(as.matrix(hfile[["assays/ADT/features"]]), "adt.genes.tsv")
#Dimensionality reduction coords
write.table(as.array(hfile[["reductions/aumap/cell.embeddings"]]), file="adt.aumap.coords.tsv", quote=FALSE, sep="\t", col.names=NA, row.names=as.array(hfile[["cell.names"]]))
write.table(as.array(hfile[["reductions/apca/cell.embeddings"]]), file="adt.apca.coords.tsv", quote=FALSE, sep="\t", col.names=NA, row.names=as.array(hfile[["cell.names"]]))
write.table(as.array(hfile[["reductions/pca/cell.embeddings"]]), file="sct.pca.coords.tsv", quote=FALSE, sep="\t", col.names=NA, row.names=as.array(hfile[["cell.names"]]))
write.table(as.array(hfile[["reductions/spca/cell.embeddings"]]), file="sct.spca.coords.tsv", quote=FALSE, sep="\t", col.names=NA, row.names=as.array(hfile[["cell.names"]]))
write.table(as.array(hfile[["reductions/umap/cell.embeddings"]]), file="sct.umap.coords.tsv", quote=FALSE, sep="\t", col.names=NA, row.names=as.array(hfile[["cell.names"]]))
write.table(as.array(hfile[["reductions/wnn.umap/cell.embeddings"]]), file="sct.wnn_umap.coords.tsv", quote=FALSE, sep="\t", col.names=NA, row.names=as.array(hfile[["cell.names"]]))
# Metadata
write.table(hfile[["meta.data"]], file="meta.tsv", quote=FALSE, sep="\t", col.names=NA, row.names=as.array(hfile[["cell.names"]]))

## On the command line ##
# Output matrices as MTX files, but converted to tsv
cbTool mtx2tsv ../orig/adt.matrix.mtx ../orig/adt.genes.tsv ../orig/cell.names.tsv exprMatrix.tsv.gz
cbTool mtx2tsv ../orig/sct.matrix.mtx ../orig/sct.genes.tsv ../orig/cell.names.tsv exprMatrix.tsv.gz

cbBuild --init

cut -f12 -d$'\t' meta.tsv |sort -u> colors.rgb.csv 
while read line; do cell=$(echo $line|cut -f1 -d ','); color=$(echo $line |cut -f2 -d ','); echo $cell,$(color-converter $color|cut -f2 -d '>' |tr -d " "); done<colors.rgb.csv > colors.csv
```
