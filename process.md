```{bash}
## extract the first column
awk -F'\t' '{print ">" $1 "\n" $1}' peptide.tsv > output.fa


## run pvacseq from official docker file
docker run -v `realpath .`:/data griffithlab/pvactools "pvacbind run /data/output.fa pep \\
        HLA-A*01:01,HLA-A*02:01,HLA-B*18:01,HLA-B*45:01,HLA-C*05:01,HLA-C*07:01,HLA-A*03:01,HLA-A*11:01,HLA-A*24:01,HLA-B*07:02 \\
        all /data/result_affnity -b 10000000 -e1 7,8,9,10 -t 28 \\
        --iedb-install-directory /opt/iedb"



```
