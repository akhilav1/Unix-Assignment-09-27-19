# UNIX Assignment

# Data Inspection

# Attributes of fang_et_al_genotypes

here is my snippet of code used for data inspection

    $ du -h fang_et_al_genotypes.txt #for file size
    
    $ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt #for number of columns
    
    $ wc -l fang_et_al_genotypes.txt #for number of lines

By inspecting this file I learned that:

 1. 6.1M size
 2. 2783 lines
 3. 984 columns


# Attributes of snp_position.txt

here is my snippet of code used for data inspection

    du -h snp_position.txt #for file size
    
    awk -F "\t" '{print NF; exit}' snp_position.txt #for columns
    
    wc -l snp_position.txt #for number of lines


By inspecting this file I learned that:

1. 38K size
2. 15  columns
3. 984 lines


# Data Processing

# Maize Data
For extracting data

code to examine column 3 of fang_et al

    sort -k3 fang_ et_ genotypes.ext | cut -f -3| uniq -c |
    column -t | wc -l
    
    awk -F "\t" '$3 ~ /Group|ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt | cut -f 1,4-986 > maizegen.txt

#for sorting after transposition 
 $ awk -f transpose.awk maizegen.txt > transposed_maizegen.txt
   

    (head -n 1 transposed_maizegen.txt && tail -n +2 transposed_maizegen.txt |sort -k1,1) > sort_transposed_maizegen.txt
    

# Teosinte Data
for extracting data
    
    awk -F "\t" '$3 ~ /Group|ZMPBA|ZMPIL|ZMMPJA/' fang_et_al_genotypes.txt | cut -f 1,4-986 > teosintegen.txt


# for sorting after transposition 
      awk -f transpose.awk teosintegen.txt > transposed_teosintegen.txt
    
    (head -n 1 transposed_teosintegen.txt && tail -n +2 transposed_teosintegen.txt |sort -k1,1) >  sort_transposed_teosintegen.txt
    
     To get column 1,3,4 in snp_position  
    cut -f 1,3,4 snp_position.txt > cut_snp_position.txt
    
# Sort without header
    (head -n 1 cut_snp_position.txt && tail -n +2 cut_snp_position.txt |sort -k1,1) > sort_cut_snp_position.txt

# To Join files
    join -1 1 -2 1 --header sort_cut_snp_position.txt -t $'\t' sort_transposed_teosintegen.txt >joined_teosintegen.txt
    
    
    join -1 1 -2 1 --header sort_cut_snp_position.txt -t $'\t' sort_transposed_maizegen.txt >joined_maizegen.txt
    
# 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular
way)
    awk 'NR==1||$2 == "unknown"' joined_teosintegen.txt > joined_teosintegen_unknown.txt

    awk 'NR==1||$2 == "unknown"' joined_maizegen.txt > joined_maizegen_unknown.txt

#1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

    awk 'NR==1||$2 == "multiple"' joined_teosintegen.txt > joined_teosintegen_multiple.txt
    
    awk 'NR==1||$2 == "multiple"' joined_maizegen.txt > joined_maizegen_multiple.txt

#10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?

 #Sort files based on increasing position values


    for i in {1..10}; do awk '$2 =='$i'' joined_maizegen.txt > maizegen_chr_"$i".txt; sort -k3,3n maizegen_chr_"$i".txt > ./Maize/maizegen_chr_"$i"_increasing.txt;done
    
    for i in {1..10}; do awk '$2 =='$i'' joined_teosintegen.txt > teosintegen_chr_"$i".txt; sort -k3,3n teosintegen_chr_"$i".txt > ./Teosinte/teosintegen_chr_"$i"_increasing.txt;done
# A for  loop is created which will examine column 2 and for integers and produce files and the concurrent output will be sorted in increasing order and produced as individual files.

# 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with
missing data encoded by this symbol: -


      for i in {1..10}; do awk '$2 =='$i'' joined_maizegen.txt; sort -k3,3nr maizegen_chr_"$i".txt | sed 's/?/-/g' > ./Maize/maizegen_chr_"$i"_decreasing.txt;done
    
    for i in {1..10}; do awk '$2 =='$i'' joined_teosintegen.txt ; sort -k3,3nr teosintegen_chr_"$i".txt | sed 's/?/-/g' > ./Teosinte/teosintegen_chr_"$i"_decreasing.txt;done

#A for  loop is created which will examine column 2 for integers and produce files and the concurrent output will be sorted in decreasing order and then "?"  will be changed to "-" and then produced as individual files.
