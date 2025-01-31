
Predict affnity (bash)

```{bash}
## extract the first column
awk -F'\t' '{print ">" $1 "\n" $1}' peptide.tsv > output.fa


## run pvacseq from official docker file
docker run -v `realpath .`:/data griffithlab/pvactools pvacbind run /data/output.fa pep \
"HLA-A*01:01,HLA-A*02:01,HLA-B*18:01,HLA-B*45:01,HLA-C*05:01,HLA-C*07:01,HLA-A*03:01,HLA-A*11:01,HLA-A*24:01,HLA-B*07:02" \
all /data/result -b 10000000 -e1 7,8,9,10 -t 28 \
--iedb-install-directory /opt/iedb



```


Summerize result to peptide level (R)

```{r}

list(endogenous = "HLA-A*01:01,HLA-A*02:01,HLA-B*18:01,HLA-B*45:01,HLA-C*05:01,HLA-C*07:01"%>%str_split_1(","),
tranfected = "HLA-A*03:01,HLA-A*11:01,HLA-A*24:01,HLA-B*07:02"%>%str_split_1(","),
unrelated = "HLA-A*29:01,HLA-B*15:02,HLA-B*46:01,HLA-B*58:01,HLA-B*38:02,HLA-B*08:01,HLA-C*08:01,HLA-C*01:02"%>%str_split_1(","))%>%
  enframe(name = "Group", value = "HLA Allele")%>%
  unnest(`HLA Allele`) -> hla

pep = read_tsv("result/MHC_Class_I/pep.all_epitopes.tsv")%>% 
  select(c(ends_with("Score"),Mutation, `HLA Allele`,`Epitope Seq`),)%>%
  group_by(Mutation, `HLA Allele`)%>%
  summarise_all(min)%>%
  ungroup()%>%
  mutate(high_affinity = `Median IC50 Score`<50)

```
