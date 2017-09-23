# UNIX_Assignment

# README

this is the README.md file for the assignment

**Document the work** in a version-controlled repository using git. 
The repository should include a README.md file in Markdown format.
The README.md file **describes the workflow** for the data inspection and data processing and all the files created.
When the assignment is finished, **send a url linking** to the GitHub repository via a Slack direct message to all three instructors.

# Data Inspection

The objective here is to describe the structure of fang_et_al_genotypes.txt and snp_position.txt files

**inspecting files broadly**

$ less fang_et_al_genotypes.txt

$ less snp_position.txt

**Number of lines, words and bytes**

$ wc  fang_et_al_genotypes.txt snp_position.txt

2783  2744038 11051939 fang_et_al_genotypes.txt
984    13198    82763 snp_position.txt
3767  2757236 11134702 total

**Only number of lines**

$ wc -l fang_et_al_genotypes.txt snp_position.txt

    2783 fang_et_al_genotypes.txt
     984 snp_position.txt
    3767 total

**File size**

$ du -lh fang_et_al_genotypes.txt snp_position.txt

11M	fang_et_al_genotypes.txt
84K	snp_position.txt
    
**Number of columns**

$ awk '{print NF; exit}' fang_et_al_genotypes.txt 

986

$ awk -F "\t" '{print NF; exit}' snp_position.txt

15

**Name of the first ten columns of fang_et_al_genotype.txt*

$ head -n 1 fang_et_al_genotypes.txt | awk '{print $1,$2,$3,$4,$5,$6,$7,$8,$9,$10}'

Sample_ID JG_OTU Group abph1.20 abph1.22 ae1.3 ae1.4 ae1.5 an1.4 ba1.6

**Name of the columns of snp_position.txt**

$ head -n 1 snp_position.txt

SNP_ID	cdv_marker_id	Chromosome	Position	alt_pos	
mult_positions	amplicon	cdv_map_feature.name	gene	candidate/random	
Genaissance_daa_id	Sequenom_daa_id	count_amplicons	count_cmf	count_gene


# Data Processing

The goal here is to process the files with UNIX. The process of formating the files is describe here.

## Extracting the genotype file

#### Extracting the genotype file for maize groups= ZMMIL,ZMMLR and ZMMMR

I used -c to know with how many information I am dealing with (number of lines);

$ grep -c 'ZMMIL' fang_et_al_genotypes.txt
290
$ grep -c 'ZMMLR' fang_et_al_genotypes.txt
1256
$ grep -c 'ZMMMR' fang_et_al_genotypes.txt
27

I keep with only the titles of the data on the file group_zmm.txt

$ head -n 1 fang_et_al_genotypes.txt > group_zmm.txt

I joined the information of the groups ZMMIL, ZMMLR, and ZMMMR genotypes to group_zmm.txt

$ awk '$3 ~ /ZMMIL|ZMMLR|ZMMMR/ {print $0}' fang_et_al_genotypes.txt >> group_zmm.txt

I keep with the columns of Sample_ID and genotypes and created the newfile group_zmm_genotype.txt

$ cut -f 1,4-986 group_zmm.txt > group_zmm_genotype.txt

Now, to sort the information by SNP_ID I transpose the group_zmm_genotype.txt file to the group_zmm_trans_genotype.txt

$ awk -f transpose.awk group_zmm_genotype.txt > group_zmm_trans_genotype.txt

Finally, I can sort the file group_zmm_trans_genotype.txt and created a new file group_zmm_trans_genotype_sorted.txt

$ tail -n +2 group_zmm_trans_genotype.txt | sort -k1,1 > group_zmm_trans_genotype_sorted.txt

I check the result of the first ten rows and the first five columns of group_zmm_trans_genotype_sorted.txt

$ head group_zmm_trans_genotype_sorted.txt | awk '{print $1,$2,$3,$4,$5}'

abph1.20 C/G C/G C/C C/G
abph1.22 A/A A/A A/A A/A
ae1.3 T/T T/T T/T G/T
ae1.4 G/G G/G G/G A/G
ae1.5 C/C C/T C/C C/T
an1.4 C/G C/G C/C C/C
ba1.6 G/G A/G G/G G/G
ba1.9 G/G G/T ?/? G/G
bt2.5 C/C C/C C/C C/C
bt2.7 ?/? ?/? G/G G/G

#### Extracting the genotype file for teosinte groups= ZMPBA,ZMPIL and ZMPJA 

For this group I repeated the same process as in the maize group:

I keep with the titles of the file

$ head -n 1 fang_et_al_genotypes.txt > group_zmp.txt

I added the selected groups to the file

$ awk '$3 ~ /ZMPBA|ZMPIL|ZMPJA/ {print $0}' fang_et_al_genotypes.txt >> group_zmp.txt

I keep with the columns of interest

$ cut -f 1,4-986 group_zmp.txt > group_zmp_genotype.txt 

I transpose the file

$ awk -f transpose.awk group_zmp_genotype.txt > group_zmp_trans_genotype.txt

Finally, I sort the file

$ tail -n +2 group_zmp_trans_genotype.txt | sort -k1,1 > group_zmp_trans_genotype_sorted.txt

I check if the file has be correctly sorted

$ head group_zmp_trans_genotype_sorted.txt | awk '{print $1,$2,$3,$4,$5}'

abph1.20 C/G C/G G/G G/G
abph1.22 A/A A/A A/A A/A
ae1.3 T/T G/T T/T T/T
ae1.4 G/G A/G G/G G/G
ae1.5 T/T T/T T/T T/T
an1.4 C/C C/C C/C C/C
ba1.6 A/G A/A A/A A/A
ba1.9 G/G G/G G/G G/G
bt2.5 T/T C/T C/T C/C
bt2.7 A/A A/A A/A A/A


### Join the two files 

#### 

Extract the first , the third and the fourth columns of the snp_position.txt and save to the snp_positon_134.txt

$ cut -f 1,3,4 snp_position.txt > snp_position_cutted.txt

check numer of columns 

$ awk '{print NF;exit}' snp_position_cutted.txt

3

check the ten rows of the snp_position_cutted.txt
$ head snp_position_cutted.txt
SNP_ID	Chromosome	Position
abph1.20	2	27403404
abph1.22	2	27403892
ae1.3	5	167889790
ae1.4	5	167889682
ae1.5	5	167889821
an1.4	1	240498509
ba1.6	3	181362952
ba1.9	3	181362666
bt2.5	4	66290049


check the sort status of the snp_position_cutted.txt

$ sort -k1,1 -c snp_position_cutted.txt

sort: snp_position_cutted.txt:2: disorder: abph1.20	2	27403404

sort the snp_position.txt according to the SNP_ID and save to the snp_position_cutted_sorted.txt file

$ tail -n +2 snp_position_cutted.txt | sort -k1,1 > snp_position_cutted_sorted.txt

check the first ten rows of the snp_position_cutted_sorted.txt

$ head snp_position_cutted_sorted.txt

abph1.20	2	27403404
abph1.22	2	27403892
ae1.3	5	167889790
ae1.4	5	167889682
ae1.5	5	167889821
an1.4	1	240498509
ba1.6	3	181362952
ba1.9	3	181362666
bt2.5	4	66290049
bt2.7	4	66290994


######################################################
#################################
###########


#### 2.2.2 join the snp_position_cutted_sorted.txt and the group_zmm_trans_genotype_sorted.txt

$ join -t $'\t' -1 1 -2 1 snp_position_cutted_sorted.txt group_zmm_trans_genotype_sorted.txt > group_zmm_with_position.txt

check the first ten rows and first five columns of the group_zmm_with_position.txt

$ head group_zmm_with_position.txt | awk '{print $1,$2,$3,$4,$5}'

abph1.20 2 27403404 C/G C/G
abph1.22 2 27403892 A/A A/A
ae1.3 5 167889790 T/T T/T
ae1.4 5 167889682 G/G G/G
ae1.5 5 167889821 C/C C/T
an1.4 1 240498509 C/G C/G
ba1.6 3 181362952 G/G A/G
ba1.9 3 181362666 G/G G/T
bt2.5 4 66290049 C/C C/C
bt2.7 4 66290994 ?/? ?/?

check the last ten rows and first five columns of the group_zmm_with_position.txt

$ tail -n 10 group_zmm_with_position.txt | awk '{print $1,$2,$3,$4,$5}'

te1.3 3 163617892 A/G G/G
te1.4 3 163617645 G/G T/T
zagl1.1 1 4912526 A/A A/A
zagl1.6 1 4835658 T/T T/T
zap1.2 2 233128584 A/G A/A
zen1.1 unknown unknown G/G G/G
zen1.2 unknown unknown A/A A/A
zen1.4 unknown unknown ?/? T/T
zfl2.6 2 12543294 G/G C/C
zmm3.4 9 16966348 C/C C/C

#### 2.2.3 join the snp_position_cutted_sorted.txt and group_zmp_trans_genotype_sorted.txt

$ join -t $'\t' -1 1 -2 1 snp_position_cutted_sorted.txt group_zmp_trans_genotype_sorted.txt > group_zmp_with_position.txt

check the first ten rows and first five columns of the group_zmp_with_position.txt

$ head group_zmp_with_position.txt | awk '{print $1,$2,$3,$4,$5}'

abph1.20 2 27403404 C/G C/G
abph1.22 2 27403892 A/A A/A
ae1.3 5 167889790 T/T G/T
ae1.4 5 167889682 G/G A/G
ae1.5 5 167889821 T/T T/T
an1.4 1 240498509 C/C C/C
ba1.6 3 181362952 A/G A/A
ba1.9 3 181362666 G/G G/G
bt2.5 4 66290049 T/T C/T
bt2.7 4 66290994 A/A A/A

check the last ten rows and first five columns of the group_zmp_with_position.txt

$ tail group_zmp_with_position.txt | awk '{print $1,$2,$3,$4,$5}'

te1.3 3 163617892 G/G G/G
te1.4 3 163617645 G/G G/T
zagl1.1 1 4912526 A/C A/C
zagl1.6 1 4835658 C/T C/T
zap1.2 2 233128584 A/A A/G
zen1.1 unknown unknown G/G G/G
zen1.2 unknown unknown G/G A/G
zen1.4 unknown unknown T/T C/C
zfl2.6 2 12543294 G/G ?/?
zmm3.4 9 16966348 C/C ?/?

### 2.3 For maize (Group = ZMMIL, ZMMLR, and ZMMR in the third column of the fang_et_al_genotypes.txt file) need to seperate into 20 files

10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by ?

$  more group_zmm_with_position.txt | sed 's/?.?/?/g' | sort -k2,2n -k3,3n | awk -F '\t' '{print >"group_zmm_with_incre_pos_chr"$2".txt"}'

check the files
$ wc *zmm*incre*
     53   83528  326406 group_zmm_with_incre_pos_chr10.txt
    155  244280  952543 group_zmm_with_incre_pos_chr1.txt
    127  200152  780527 group_zmm_with_incre_pos_chr2.txt
    107  168632  659471 group_zmm_with_incre_pos_chr3.txt
     91  143416  559941 group_zmm_with_incre_pos_chr4.txt
    122  192272  755862 group_zmm_with_incre_pos_chr5.txt
     76  119776  469119 group_zmm_with_incre_pos_chr6.txt
     97  152872  597395 group_zmm_with_incre_pos_chr7.txt
     62   97712  382559 group_zmm_with_incre_pos_chr8.txt
     60   94560  370064 group_zmm_with_incre_pos_chr9.txt
      6    9450   37068 group_zmm_with_incre_pos_chrmultiple.txt
     27   42552  166473 group_zmm_with_incre_pos_chrunknown.txt
    983 1549202 6057428 total
    
10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by -
$ more group_zmm_with_position.txt | sed 's/?.?/-/g' | sort -k2,2n -k3,3nr | awk -F '\t' '{print >"group_zmm_with_decre_pos_chr"$2".txt"}'

check the files
$ wc *zmm*de*
     53   83528  326406 group_zmm_with_decre_pos_chr10.txt
    155  244280  952543 group_zmm_with_decre_pos_chr1.txt
    127  200152  780527 group_zmm_with_decre_pos_chr2.txt
    107  168632  659471 group_zmm_with_decre_pos_chr3.txt
     91  143416  559941 group_zmm_with_decre_pos_chr4.txt
    122  192272  755862 group_zmm_with_decre_pos_chr5.txt
     76  119776  469119 group_zmm_with_decre_pos_chr6.txt
     97  152872  597395 group_zmm_with_decre_pos_chr7.txt
     62   97712  382559 group_zmm_with_decre_pos_chr8.txt
     60   94560  370064 group_zmm_with_decre_pos_chr9.txt
      6    9450   37068 group_zmm_with_decre_pos_chrmultiple.txt
     27   42552  166473 group_zmm_with_decre_pos_chrunknown.txt
    983 1549202 6057428 total

### 2.4 For teosinte (Group = ZMPBA, ZMPIL, and ZMPJA in the third column of the fang_et_al_genotypes.txt file) need to seperate into 20 files:
10 files (1 for each chromosome) with SNPs ordered based on increasing positions values and with missing data encoded by ?
$ more group_zmp_with_position.txt | sed 's/?.?/?/g' | sort -k2,2n -k3,3n | awk -F '\t' '{print >"group_zmp_with_incre_pos_chr"$2".txt"}'

check the files
wc *zmp*incre*
     53   51834  203652 group_zmp_with_incre_pos_chr10.txt
    155  151590  593685 group_zmp_with_incre_pos_chr1.txt
    127  124206  486803 group_zmp_with_incre_pos_chr2.txt
    107  104646  410133 group_zmp_with_incre_pos_chr3.txt
     91   88998  347659 group_zmp_with_incre_pos_chr4.txt
    122  119316  469278 group_zmp_with_incre_pos_chr5.txt
     76   74328  291107 group_zmp_with_incre_pos_chr6.txt
     97   94866  373563 group_zmp_with_incre_pos_chr7.txt
     62   60636  238051 group_zmp_with_incre_pos_chr8.txt
     60   58680  230668 group_zmp_with_incre_pos_chr9.txt
      6    5862   23214 group_zmp_with_incre_pos_chrmultiple.txt
     27   26406  103275 group_zmp_with_incre_pos_chrunknown.txt
    983  961368 3771088 total

10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by -
$ more group_zmp_with_position.txt | sed 's/?.?/-/g' | sort -k2,2n -k3,3nr | awk -F '\t' '{print >"group_zmp_with_decre_pos_chr"$2".txt"}'

check the files
$ wc *zmp*de*
     53   51834  203652 group_zmp_with_decre_pos_chr10.txt
    155  151590  593685 group_zmp_with_decre_pos_chr1.txt
    127  124206  486803 group_zmp_with_decre_pos_chr2.txt
    107  104646  410133 group_zmp_with_decre_pos_chr3.txt
     91   88998  347659 group_zmp_with_decre_pos_chr4.txt
    122  119316  469278 group_zmp_with_decre_pos_chr5.txt
     76   74328  291107 group_zmp_with_decre_pos_chr6.txt
     97   94866  373563 group_zmp_with_decre_pos_chr7.txt
     62   60636  238051 group_zmp_with_decre_pos_chr8.txt
     60   58680  230668 group_zmp_with_decre_pos_chr9.txt
      6    5862   23214 group_zmp_with_decre_pos_chrmultiple.txt
     27   26406  103275 group_zmp_with_decre_pos_chrunknown.txt
    983  961368 3771088 total
Total of 40 files were produced.

Classify the 40 files in to different folders

$ mkdir zm{m,p}
$ mv group_zmp*.* zmp/
$ mv group_zmm*.* zmm/

$ cd zmp/
$ mkdir group_zmp{incre,decre}
$ mv *incre*.* group_zmpincre/
$ mv *decre*.* group_zmpdecre/

$ cd zmm/
$ mkdir group_zmm{incre,decre}
$ mv *incre*.* group_zmmincre/
$ mv *decre*.* group_zmmdecre/

check the files and folders

$ ls -R
.:
**group_zmmdecre**          group_zmm_trans_genotype_sorted.txt  group_zmm_with_position.txt
group_zmm_genotype.txt  group_zmm_trans_genotype.txt         UNIX_Assignment.md
**group_zmmincre**          group_zmm.txt                        UNIX_Assignment.pdf

./group_zmmdecre:
group_zmm_with_decre_pos_chr10.txt  group_zmm_with_decre_pos_chr4.txt  group_zmm_with_decre_pos_chr8.txt
group_zmm_with_decre_pos_chr1.txt   group_zmm_with_decre_pos_chr5.txt  group_zmm_with_decre_pos_chr9.txt
group_zmm_with_decre_pos_chr2.txt   group_zmm_with_decre_pos_chr6.txt  group_zmm_with_decre_pos_chrmultiple.txt
group_zmm_with_decre_pos_chr3.txt   group_zmm_with_decre_pos_chr7.txt  group_zmm_with_decre_pos_chrunknown.txt

./group_zmmincre:
group_zmm_with_incre_pos_chr10.txt  group_zmm_with_incre_pos_chr4.txt  group_zmm_with_incre_pos_chr8.txt
group_zmm_with_incre_pos_chr1.txt   group_zmm_with_incre_pos_chr5.txt  group_zmm_with_incre_pos_chr9.txt
group_zmm_with_incre_pos_chr2.txt   group_zmm_with_incre_pos_chr6.txt  group_zmm_with_incre_pos_chrmultiple.txt
group_zmm_with_incre_pos_chr3.txt   group_zmm_with_incre_pos_chr7.txt  group_zmm_with_incre_pos_chrunknown.txt

