###Original file location
setwd("H:/20240729ssgsea")
library(GSVA)
library(pheatmap)
library(tidyverse)
library(clusterProfiler)
library(org.Mm.eg.db)
GEP<-read.csv("R20240729.csv")
load("H:/ssgsea/cellmarkergene.Rdata")
gep_symbol<-GEP
gep_symbol=gep_symbol[!duplicated(gep_symbol[,1]),]
rownames(gep_symbol)<-gep_symbol[,1]
gep_symbol<-gep_symbol[,-1]
###ssgsea count pick Possion
ssgsea.result<-gsva(as.matrix(gep_symbol),kcdf="Poisson",cellmarkergene,method="ssgsea")
###Standardisation of enrichment results
ssgsea.result.1 <- ssgsea.result
for (i in colnames(ssgsea.result)) {
  ssgsea.result.1[,i] <- (ssgsea.result[,i] -min(ssgsea.result[,i]))/(max(ssgsea.result[,i] )-min(ssgsea.result[,i] ))
}

heatmap1<-pheatmap(ssgsea.result.1,
                   main = "ssgsea.result.1",
                   cluster_cols = FALSE, cluster_rows = FALSE,
                   show_colnames = TRUE,
                   angle_col = 45,  # Rotate x-axis labels by 45 degrees
                   cellwidth = 20, cellheight = 20,
                   fontsize = 18
)
pdf("ssgsearesult1.pdf",width = 20,height = 10)
heatmap1
dev.off()
write.csv(ssgsea.result,"ssgsea.result.csv")
write.csv(ssgsea.result.1,"ssgsea.result.1.csv")

###Add marker
###AA is cell name, followed by the gene name.
cellmarkergene$AA<-c("Syne1","Synm","Tead1","Tlcd1", "Tril","Trim9","Vegfa","Vim","Wnt3")
