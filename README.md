# Unix-Assignment
## Unix Assignment

### Data Inspection

Attributes of Snp_positions.txt

cat snp_positions.txt

head snp_positions.txt

head -n 3 snp_positions.txt

tail snp_positions.txt

tail -n +2 snp_positons.txt

wc snp_positions.txt

wc -l snp_positions.txt

du -h snp_positions.txt

awk -F "\t" '{print NF; exit}' snp_positions.txt

#### After inspecting the contents within the .txt file, I learned: That the file had 984 lines, 13,198 words, and 82,763 bytes. That the file was 84K. The file had 15 columns, although the last column was not labelled.

Attributes of fang_et_al_genotypes.txt

cat fang_et_al_genotypes.txt

head fang_et_al_genotypes.txt

tail fang_et_al_genotypes.txt

wc fang_et_al_genotypes.txt

du -h fang_et_al_genotypes.txt

awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt

#### After inspecting the contents of fang_et_al_genotypes.txt, I learned: This file is a string, using a "/" as a delimiter. There is missing data represented by "?". That the file had 2,783 lines, 2,744,038 words, and 11,051,939 bytes. The file is 11M. The file has 986 columns

### Data Processing

#### Use the grep() command to extract the specified groups for the maize and teosinte genotypes that are located in the fang_et_al_genotypes.txt file and save as a new file named maize.txt and teosinte.txt. Specifically, using -E indicates to search the file for any line indicating ZMMIL, ZMMLR, ZMMMR, ZMBA, ZMPIL, or ZMPJA. This -E also allows us to use the "|" operator.

grep -E 'ZMMIL|ZMMLR|ZMMMR' fang_et_al_genotypes.txt > maize.txt

grep -E 'ZMBA|ZMPIL|ZMPJA' fang_et_al_genotypes.txt > teosinte.txt

#### Using sort() to sort the snp_position.txt file by the content in the first column as indicated by -k1
head -n 1 snp_position.txt > snp_positons_sorted.txt
sort -k1 snp_positions.txt >> snp_positions_sorted.txt

#### Using awk() to transpose the teosinte.txt file so that columns become rows while also creating a new file named transposed_teosinte_genotypes.txt

awk -f transpose.awk teosinte.txt > transposed_teosinte_genotypes.txt

#### Using awk() to transpose the maize.txt file

awk -f transpose.awk maize.txt > transposed_maize_genotypes.txt

#### Use join() to merge the transposed data files for maize and teosinte with the snp_positions_sorted.txt file. More specifically, (-1 1) means to join the first column of the first file indicated in the code, and (-2 1) means join the first column in the second file indicated in the code. (-a, 2) means to join all of the lines from the second file regardless of if the content matches anything from the snp's txt file

join -1 1 -2 1 -a 2 snp_positions_sorted.txt sorted_maize.txt > joined_maize.txt

join -1 1 -2 1 -a 2 snp_positions_sorted.txt sorted_teosinte.txt > joined_teosinte.txt

#### Use join() to merge the maize and teosinte files that were just merged with snp data into a new file called final_genotype_data.txt. More specifically, the (-1 1) and (-2 1) are indicating to join based on the first column of both file 1 and file 2 as indinated by their position in the line of code.

join -1 1 -2 1 joined_maize.txt joined_teosinte.txt > final_genotype_data.txt

#### For this next step, I did some research to figure out how to create a for-loop. I did this so that I could create individual files for the 10 chromosomes without having to individually write awk commands. This initial line created 10 txt files using the joined_maize.txt and also replaces any missing values with "?". However, the missing values are not necessary to replace because they are already represented as a "?"

for i in {1..10}; do
    awk -v chr="$i" '$3 == chr' joined_maize.txt | sed 's/NA/?/g' | sort -k2,2n > "chr${i}_increasing.txt"
done

#### Same code but for teosinte (10 files created, increasing order, no replacements)

for i in {1..10}; do
    awk -v chr="$i" '$3 == chr' joined_teosinte.txt | sed 's/NA/?/g' | sort -k2,2n > "chr${i}_increasing.txt"
done

#### Now, for maize data, create 10 files in decreasing order and missing values "?" are now represeneted as "-"

for i in {1..10}; do
    awk -v chr="$i" '$3 == chr' joined_maize.txt | sed 's/?/-/g' | sort -k2,2nr > "chr${i}_decreasing.txt"
done

#### Same code but for teosinte (10 files created, decreasing order, missing values "?" replaced by "-")

for i in {1..10}; do
    awk -v chr="$i" '$3 == chr' joined_teosinte.txt | sed 's/?/-/g' | sort -k2,2nr > "chr${i}_decreasing.txt"
done

#### To elaborate further, in the code for increasing order, "sed 's/\bNA\b/?/g'" is telling the computer to serch for "NA" and replace it with "?". This is for both maize and teosinte data. I put NA because there are no missing values labeled that and since the missing values are represented by "?" already, there was no reason to do this step but I wanted to show that you could.
#### For the decreasing code, I indicate that I want the computer to search for any "?" and replace it with "-" as seen by this section of code: "sed 's/?/-/g'".
#### Finally, for the increasing code, this part "sort -k2,2n" indicates that I want the data sorted numerically in increasing order. For decreasing order, we add an "r" to indicate "reverse" numerical order as seen by "sort -k2, 2nr".

#### create a file with all unknown positions for maize, specifically looking at column 3 as indicated by '$3 == "?"'. I have no idea what happened, but it keeps creating blank txt files but I thought that the SNP position is in column 3 so I'm not sure what is going on. So I'm assuming I did something wrong somewhere.
awk '$3 == "?"' joined_maize.txt > unknown_positions_maize.txt

#### same thing but teosinte. Same issue too.
awk '$3 == "?"' final_genotype_data.txt > unknown_positions_teosinte.txt

#### This is saying to sort the maize file and only display the lines that duplicated in the data file indicated
sort joined_maize.txt | uniq -d

#### same thing but with teosinte
sort joined_teosinte.txt | uniq -d

#### I believe this was everything that was supposed to be done. I definitely don't think that I did this in the best way possible and things could have definitely been cleaned up but I did it the best way that I could.








