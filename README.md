#  Dsuite
Fast calculation of D-statistics and related admixture evidence directly from VCF files.

## Compilation

To compile you must have a reasonably recent GCC or clang compiler (on mac OS this comes with Command Line Tools) and the zlib compression library (https://www.zlib.net). Both should already be present on most systems. 


```
git clone https://github.com/millanek/Dsuite.git
cd Dsuite
make
```

The Dsuite executable will be in the Build folder, so to run it type e.g. `./Build/Dsuite`; this will show the available commands. To execute e.g. the Dtrios command, type `./Build/Dsuite Dtrios`.

## Input files:
### Required files:
1. Bialllelic variants in a VCF file. The file can be gzipped.
http://www.internationalgenome.org/wiki/Analysis/Variant%20Call%20Format/vcf-variant-call-format-version-40/
2. Population/species map `SETS.txt`: a text file with one individual per row and a tab separating the individual’s name from the name of the species/population it belongs to, as shown below:
```
Ind1    Species1
Ind2    Species1
Ind3    Species2
Ind4    Species2
Ind5    Species3
...     ...
IndN    Species_n
```
### Optional files:
3. A tree in Newick format. For now I only have a very basic Newick format parser. Therefore, the tree should only have leaf labels and no branch lengths.
4. The `test_trios.txt` file for `Dinvestigate`. One trio of populations/species per line, separated by a tab in the order `P1  P2  P3`:
```
Species1    Species2    Species3
Species1    Species4    Species2
...         ...         ...
```
## Commands:
### Dsuite Dtrios - Calculate D statistics (ABBA-BABA) for all possible trios of populations/species
```
Usage: Dsuite Dtrios [OPTIONS] INPUT_FILE.vcf SETS.txt
Calculate the Dmin-statistic - the ABBA/BABA stat for all trios of species in the dataset (the outgroup being fixed)
the calculation is as definded in Durand et al. 2011
The SETS.txt should have two columns: SAMPLE_ID    SPECIES_ID
The outgroup (can be multiple samples) should be specified by using the keywork Outgroup in place of the SPECIES_ID

-h, --help                              display this help and exit
-j, --JKwindow                          (default=20000) Jackknife block size in SNPs
-r , --region=start,length              (optional) only process a subset of the VCF file
-t , --tree=TREE_FILE.nwk               (optional) a file with a tree in the newick format specifying the relationships between populations/species
                                        D values for trios arranged according to these relationships will be output in a file with _tree.txt suffix
-n, --run-name                          run-name will be included in the output file name
```

### DtriosCombine - Combine results from Dtrios runs across genomic regions (e.g. per-chromosome)
```
Usage: Dsuite DtriosCombine [OPTIONS] DminFile1 DminFile2 DminFile3 ....
Combine the BBAA, ABBA, and BABA counts from multiple files (e.g per-chromosome) and output the overall Dmin stats
also the D stats for the trio arrangement where the BBAA is the most common pattern

-h, --help                              display this help and exit
-n, --run-name                          run-name will be included in the output file name
-s , --subset=start,length              (optional) only process a subset of the trios
```
###  Dinvestigate - Follow up analyses for trios with significantly elevated D: calculates the f4 statistic, and also f_d and f_dM in windows along the genome
```
Usage: Dsuite Dinvestigate [OPTIONS] INPUT_FILE.vcf.gz SETS.txt test_trios.txt
Calculate the admixture proportion estimates f_G, f_d (Martin et al. 2014 MBE), and f_dM (Malinsky et al., 2015)
Also outputs f_d and f_dM in genomic windows
The SETS.txt file should have two columns: SAMPLE_ID    POPULATION_ID
The test_trios.txt should contain names of three populations for which the statistics will be calculated:
POP1   POP2    POP3
There can be multiple lines and then the program generates multiple ouput files, named like POP1_POP2_POP3_localFstats_SIZE_STEP.txt

-h, --help                              display this help and exit
-w SIZE, --window=SIZE,STEP             (required) D, f_D, and f_dM statistics for windows containing SIZE useable SNPs, moving by STEP (default: 50,25)
-n, --run-name                          run-name will be included in the output file name
```
