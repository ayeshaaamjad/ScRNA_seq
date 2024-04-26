# ScRNA_seq
Basics of Single cell RNA sequencing analysis
#THE PROCESS OF OBTAINING SINGLE CELL DATA FROM THE BIOLOGICAL SAMPLES
#In a tissue there are multiple cells, in order to know which cell type is having transcriptional changes we have to perform single cell RNA_SEQ on an invitro culture that only cntains one cell type
#the most popular method is 10x droplet based single cell sequencing.the other methods are plate-based, Nanowell, Combinatorial barcoding.
# whether the method is plate based or 10X droplet based, error may occur which causes multiple cells being captured together(doublets or muliplets), non-viable cells captured, or no cells being captured at all (empty droplets or wells).
# Empty droplets are specially common as droplet-based methods rely on low concentration flow of input cells to control doublets rates. 
#steps of this procedure are as follows
# 1. we have 10x beads with attached barcodes are squirt through the biofluidics system with the cells from the sample
# 2. the beads get partitioned into little droplets so the cells will be seperated in little droplets with one of the barcodes beads. and since they are suspended in oil (microfuidics) they cannot combine with other cells
# 3. then they are processed individually in the downstream reactions such as RT and PCR
# libraries are contructed in which intracellular mRNA is captured, reverse-transcribed to cDNA molecules and amplified.
#  As cells undergo this process in isolation, the mRNA from each cell can be labelled with a well‐ or droplet‐specific cellular barcod. 
# 4. In the end we have a library with barcode such as 10x barcode, which is a string of known nucleotide which can identify which cell this read came from. second barcode is UMI which is Unique Molecular Identifier, it is not specific to individual cells but it gets amplified in the PCR so that PCR duplicates and actual gene copies can be differentiated.
# 5. then we analyse the data, a table came which has rows and columns, this table is a count matrix.
# each column is the data from individual cells and each columns are the data from individual genes. and the entries are the number of UMI
# 6. the above created libraries are pooled together(multiplexed) for sequencing, this 
# 7. Sequencing produces read data, which undergo quality control, grouping based on their assigned barcodes (demultiplexing) and alignment in read processing pipelines. For UMI‐based protocols, read data can be further demultiplexed to produce counts of captured mRNA molecules (count data).
#ANALYSING THE DATA
# 1. Before analysing the data we performed a QC process to make sure that cellular barcode data corresponds to viable cells.
# it is the step  I performed during analysing known as metadata. this data set has three components number of counts per barcode mentioned in the table as n_counts, the other number of genes per barcode mentioned aS n_Feature_RNA, and the fraction of counts from mitochondrial genes per barcode which I calculated later as percentage.mt using the function PercentageFeatureSet
# then we create vlonplot to observe the data we can also observe it by plotting histogram. In the plot the cells which are towards the peaks are the dying or sick cells or doublets. 
# barcodes with a low count depth, few detected genes, and a high fraction of mitochondrial counts are indicative of cells whose cytoplasmic mRNA has leaked out through a broken membrane, and thus, only mRNA located in the mitochondria is still conserved
# In contrast, cells with unexpectedly high counts and a large number of detected genes may represent doublets.
# 3. Thus, high‐count depth thresholds are commonly used to filter out potential doublets(still needs to perform with a package doublefinder),low quality cells were filtered. 
# Each count in a count matrix represents the successful capture, reverse transcription and sequencing of a molecule of cellular mRNA
# Count depths for identical cells can differ due to the variability inherent in each of these steps. Thus, when gene expression is compared between cells based on count data, any difference may have arisen solely due to sampling effects.
# 4.  Normalization addresses this issue by e.g. scaling count data to obtain correct relative gene expression abundances between cells.
# for this purpose counts per million CPM normalisation was used using the function NormalizedData
# After normalization, data matrices are typically log(x+1)‐transformed. This transformation has three important effects
# 1. Firstly, distances between log‐transformed expression values represent log fold changes, which are the canonical way to measure changes in expression
# 2. Secondly, log transformation mitigates (but does not remove) the mean–variance relationship in single‐cell dat.
# 3. Finally, log transformation reduces the skewness of the data to approximate the assumption of many downstream analysis tools that the data are normally distributed. While scRNA‐seq data are not in fact log‐normally distributed, these three effects make the log transformation a crude, but useful tool. 
# Normalization as described above attempts to remove the effects of count sampling. However, normalized data may still contain unwanted variability.
# Data correction targets further technical and biological covariates such as batch, dropout, or cell cycle effects. These covariates are not always corrected for.
# Instead, the decision of which covariates to consider will depend on the intended downstream analysis.
# In my analysis I regressed out biological effects using ScaleData function.
# In order to reduce the dimensionality and keep the most variable feature (features which are informative) and fot this the function FindVariableFeatures, features were set between 200 to 2000
# After feaure selection dimension is reduced by dimensionality reduction linear dimensionality reduction was used using the function principal component analysis (PCA)
# Principal component analysis is a linear approach that generates reduced dimensions by maximizing the captured residual variance in each further dimension. 
# While PCA does not capture the structure of the data in few dimensions as well as non‐linear methods, it is the basis of many currently available analysis tools for clustering or trajectory inference.
# Indeed, PCA is commonly used as a pre‐processing step for non‐linear dimensionality reduction methods.
# After pre-processed data, downstream analysis is done
# elbowplot was plotted to choose the components with high percentage variance.
#DOWN STREAM ANALYSIS
# 1. CLUSTERING
# cluster analysis methods attempt to explain the heterogeneity in the data based on a categorization of cells into groups. 
# Organizing cells into clusters is typically the first intermediate result of any single‐cell analysis. Clusters allow us to infer the identity of member cells.
# Clusters are obtained by grouping cells based on the similarity of their gene expression profiles.
# Expression profile similarity is determined via distance metrics, which often take dimensionality‐reduced representations as input.
# now we want our cells to assign in clusters so we are using the function findClusters as our first parameter and for our second parameter is resolution
# lower the resolution higher the clusters, higher the resolution lower the clusters
# to see which resolution works the best we use resolution from 0-1 to have our cells in different clusters
# different columns will be created in meta data for each resolution
# to visualise data UMAP was plotted
