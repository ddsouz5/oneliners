# Bioinformatics one-liners

[![DOI](https://zenodo.org/badge/3882/stephenturner/oneliners.svg)](https://zenodo.org/badge/latestdoi/3882/stephenturner/oneliners)

Useful bash one-liners useful for bioinformatics (and [some, more generally useful](#etc)).  


## Contents

- [Sources](#sources)
- [Basic perl](#basic-perl)
- [Basic awk & sed](#basic-awk--sed)
- [awk, perl, datamash, R Data Operations](#awk-perl-datamash-r-data-operations)
- [awk, bioawk and sed for bioinformatics](#awk-bioawk-and-sed-for-bioinformatics)
- [sort, uniq, cut, etc.](#sort-uniq-cut-etc)
- [find, xargs, exec and GNU parallel](#find-xargs-exec-and-gnu-parallel)
- [seqtk](#seqtk)
- [GFF3 Annotations](#gff3-annotations)
- [Other generally useful aliases for your .bashrc](#other-generally-useful-aliases-for-your-bashrc)
- [Etc.](#etc)


## Sources

* <http://gettinggeneticsdone.blogspot.com/2013/10/useful-linux-oneliners-for-bioinformatics.html#comments>
* <http://sed.sourceforge.net/sed1line.txt>
* <https://github.com/lh3/seqtk>
* <http://lh3lh3.users.sourceforge.net/biounix.shtml>
* <http://genomespot.blogspot.com/2013/08/a-selection-of-useful-bash-one-liners.html>
* <http://biowize.wordpress.com/2012/06/15/command-line-magic-for-your-gene-annotations/>
* <http://genomics-array.blogspot.com/2010/11/some-unixperl-oneliners-for.html>
* <http://bioexpressblog.wordpress.com/2013/04/05/split-multi-fasta-sequence-file/>
* <http://www.commandlinefu.com/>
* <https://unix.stackexchange.com/questions/112023/how-can-i-replace-a-string-in-a-files>
* <https://www.gnu.org/software/datamash/alternatives/>
* <https://www.biostars.org/p/63816/>


## Basic perl

[[back to top](#contents)]

Substitution conditional. -p argument ensures that the code is executed on every line of input and
that the line is printed after execution The -i argument ensures that file is edited in-place, .bak ensures there's a backup file before change is made. -e means execute code

    perl -pi.bak -e 's/you/me/g if /we/' file

Find palindromes in unix dict

    perl -lne 'print if $_ eq reverse' /usr/share/dict/words


## Basic awk & sed

[[back to top](#contents)]

Print everything except the first line

    awk 'NR>1' input.txt
    tail --quiet -n+2 file.txt file2.txt file3.txt

Print rows 20-80:

    awk 'NR>=20&&NR<=80' input.txt

Extract fields 2, 4, and 5 from file.txt:

    awk '{print $2,$4,$5}' input.txt

Print each line where the 5th field is equal to ‘abc123’:

    awk '$5 == "abc123"' file.txt

Print each line where the 5th field is *not* equal to ‘abc123’:

    awk '$5 != "abc123"' file.txt

Print each line whose 7th field matches the regular expression:

    awk '$7  ~ /^[a-f]/' file.txt

Print each line whose 7th field *does not* match the regular expression:

    awk '$7 !~ /^[a-f]/' file.txt

Get unique entries in file.txt based on column 2 (takes only the first instance):

    awk '!arr[$2]++' file.txt

Print rows where column 3 is larger than column 5 in file.txt:

    awk '$3>$5' file.txt

Sum column 1 of file.txt:

    awk '{sum+=$1} END {print sum}' file.txt
    perl -lane '$sum += $F[0]; END { print $sum }'

Compute the mean of column 2:

    awk '{x+=$2}END{print x/NR}' file.txt

Display a Block of Text between Two Strings. For sed,the -n option suppresses the duplicate rows generated by the /p flag and prints the replaced lines only one time:

    awk '/start-pattern/,/stop-pattern/' file.txt
    sed -n '/start-pattern/,/stop-pattern/p' file.txt

Remove duplicate entries in a file without sorting

    awk '!x[$0]++' <file>

Compare parts of strings using awk split command

    cat > example.txt
    apple red	melon green
    banana yellow	kiwi brown
    mango yellow    apricot yellow
    strawberry red  strawberry juicy

    awk -F"\t" '{split($1,a," "); split($2,b," ")} a[2] == b[2]' sample.txt
    
Replace/convert white space to tab

    expand (cmd line)
    unexpand (cmd line)
    awk -v OFS="\t" '$1=$1' file1
    sed 's/[:blank:]+/,/g' thefile.txt > the_modified_copy.txt
    tr ' ' \\t < someFile > someFile

csv to tab-delimited

    awk -F"," -v OFS="\t" {print $1,$2,$3}
    
Search pattern and replace that pattern by adding some extra characters to it. '&' is the matched string

    sed 's/java/H&H/' example.txt

Replace only if the string is found in a certain context. Replace foo with bar only if there is a baz later on the same line

    sed 's/foo\(.*baz\)/bar\1/' file

Delete a Block of Text between Two Strings (inclusive)

    sed '/start-pattern/,/stop-pattern/d' file

Replace all occurances of `foo` with `bar` in file.txt:

    sed 's/foo/bar/g' file.txt
  
Replace foo with bar only on the 4th line (used -i for all 3 commands to replace in place):

    sed '4s/foo/bar/g' file
    gawk inplace 'NR==4{gsub(/foo/,"baz")};1' file
    perl -pe 's/foo/bar/g if $.==4' file

Replace multiple patterns with the same string. Replace any of foo, bar or baz with foobar
    
    sed -E 's/foo|bar|baz/foobar/g' file
    perl -pe 's/foo|bar|baz/foobar/g' file


Trim leading whitespaces and tabulations in file.txt:

    sed 's/^[ \t]*//' file.txt
    :%s/^\s\+//e (vim)
    
Trim trailing whitespaces and tabulations in file.txt:

    sed 's/[ \t]*$//' file.txt
    :%s/\s\+$//e (vim)
    
Trim leading and trailing whitespaces and tabulations in file.txt:

    sed 's/^[ \t]*//;s/[ \t]*$//' file.txt

Delete blank lines in file.txt:

    sed '/^$/d' file.txt
    grep '.'
    awk NF
    awk '/./'
    perl -ne 'print unless /^$/'

Deletes lines three through six and sends the result to the standard output

    sed 3,6d /etc/passwd

Deletes range of lines and modify input file in place

    sed -i <file> -re '<start>,<end>d'

Delete everything after and including a line containing `EndOfUsefulData`:

    sed -n '/EndOfUsefulData/,$!p' file.txt

Print a specific line (e.g. line 42) from a file:

    sed -n 42p <file>
    sed '42!d' <file>
    
Print lines 47 to 108 from a file:

    sed '47,108!d' <file>
 
Add line after,before or change line after match is found

    sed '/java/ a "Add a new line"' example.txt
    sed '/java/ i "New line"' example.txt
    sed '/java/ c "Change line"' example.txt

Print everything AFTER match, not including match

    awk '/yahoo/{y=1;next}y' data.txt
    sed '1,/yahoo/d' data.txt


Print everything BEFORE match, not including match

    awk '/pattern/ {exit} {print}' filename
    sed '/pattern/Q' filename
 
Print up to and including the match:

    awk '{print} /pattern/ {exit}' filename
    sed '/pattern/q' filename

Print everything BEFORE match, including match

    sed '/PATTERN/q' FILE

Insert line before a pattern (<http://www.theunixschool.com/2012/06/insert-line-before-or-after-pattern.html>)

    awk '/Fedora/{print "Cygwin"}1' file.txt
    sed 's/.*Fedora.*/Cygwin\n&/' file.txt
    perl -plne 'print "Cygwin" if(/Fedora/);' file.txt

Insert line after a pattern (<http://www.theunixschool.com/2012/06/insert-line-before-or-after-pattern.html>)

    awk '/Fedora/{print;print "Cygwin";next}1' file.txt
    sed 's/.*Fedora.*/&\nCygwin/' file.txt
    perl -lne 'print $_;print "Cygwin" if(/Fedora/);' file.txt

Add/append to the end of lines containing a pattern with sed or awk

    awk '/pattern/ {$0=$0" appendstring"} 1' file
    sed '/pattern/ s/$/ appendstring/' file


## awk, perl, datamash, R Data Operations

[[back to top](#contents)]


sum
    
    seq 10 | datamash sum 1
    seq 10 | awk '{sum+=$1} END {print sum}' 
    
minimum value
    
    seq -5 1 7 | datamash min 1
    seq -5 1 7 | awk 'NR==1 {min=$1} NR>1 && $1<min { min=$1 } END {print min}' 

maximum value
    
    seq -5 -1 | datamash max 1
    seq -5 -1 | awk 'NR==1 {max=$1} NR>1 && $1>max { max=$1 } END {print max}' 
    
mean
    
    seq 10 | datamash mean 1
    seq 10 | awk '{sum+=$1} END {print sum/NR}' 

For examples below
    
    DATA=$(printf "%s\t%d\n" a 1 b 2 a 3 b 4 a 3 a 6)
    
First value of each group

    echo "$DATA" | datamash -s -g 1 first 2
    echo "$DATA" | awk '!($1 in a){a[$1]=$2} END {for(i in a) { print i, a[i] }}' 
    
Last value of each group:    

    echo "$DATA" | datamash -s -g 1 last 2
    echo "$DATA" | awk '{a[$1]=$2} END {for(i in a) { print i, a[i] }}'
    
Number of values in each group

    echo "$DATA" | datamash -s -g 1 count 2
    echo "$DATA" | awk '{a[$1]++} END {for(i in a) { print i, a[i] }}'  
    
Collapse all values in each group    
    
    echo "$DATA" | datamash -s -g1 collapse 2
    echo "$DATA" | perl -lane '{push @{$a{$F[0]}},$F[1]} END{print join("\n",map{"$_ ".join(",",@{$a{$_}})} sort keys %a);}' 
    
Collapse unique values in each group    

    echo "$DATA" | datamash -s -g1 unique 2
    echo "$DATA" | perl -lane '{$a{$F[0]}{$F[1]}=1} END{print join("\n",map{"$_ ".join(",",sort keys %{$a{$_}})} sort keys %a);}'
    
Print a random value from each group
    
    echo "$DATA" | datamash -s -g 1 rand 2
    echo "$DATA" | perl -lane '{ push @{$a{$F[0]}},$F[1] } END{ print join("\n",map{"$_ ".$a{$_}->[rand(@{$a{$_}})] } sort keys %a ) ;}'
    
simple summary of the data

    echo "$DATA" | datamash min 2 q1 2 median 2 mean 2 q3 2 max 2
    echo "$DATA" | Rscript -e 'summary(read.table("stdin"))

simple summary of the data, with grouping

    echo "$DATA" | datamash -s --header-out -g 1 min 2 q1 2 median 2 mean 2 q3 2 max 2 | expand -t 18
    echo "$DATA" | Rscript -e 'a=read.table("stdin")' -e 'aggregate(a$V2,by=list(a$V1),summary)'

Calculating mean and standard-deviation for each group

    echo "$DATA" | datamash -s -g1 mean 2 sstdev 2
    echo "$DATA" | Rscript -e 'a=read.table("stdin")' -e 'f=function(x){c(mean(x),sd(x))}' -e 'aggregate(a$V2,by=list(a$V1),f)'
    
Reverse columns/fields
    
    echo "$DATA" | datamash reverse
    echo "$DATA" | perl -lane 'print join(" ", reverse @F)'
    
Transpose a file (swap rows and columns)

    echo "$DATA" | datamash transpose
    echo "$DATA" | Rscript -e 'write.table(t(read.table("stdin")),quote=F,col.names=F,row.names=F)'
    
## awk, bioawk and sed for bioinformatics

[[back to top](#contents)]


Get read length distribution of FASTQ file

    awk '{if(NR%4==2) print length($1)}'


Returns all lines on Chr 1 between 1MB and 2MB in file.txt. (assumes) chromosome in column 1 and position in column 3 (this same concept can be used to return only variants that above specific allele frequencies):

    cat file.txt | awk '$1=="1"' | awk '$3>=1000000' | awk '$3<=2000000'


Basic sequence statistics. Print total number of reads, total number unique reads, percentage of unique reads, most abundant sequence, its frequency, and percentage of total in file.fq:

    cat myfile.fq | awk '((NR-2)%4==0){read=$1;total++;count[read]++}END{for(read in count){if(!max||count[read]>max) {max=count[read];maxRead=read};if(count[read]==1){unique++}};print total,unique,unique*100/total,maxRead,count[maxRead],count[maxRead]*100/total}'


Convert .bam back to .fastq:

    samtools view file.bam | awk 'BEGIN {FS="\t"} {print "@" $1 "\n" $10 "\n+\n" $11}' > file.fq


Keep only top bit scores in blast hits (best bit score only):

    awk '{ if(!x[$1]++) {print $0; bitscore=($14-1)} else { if($14>bitscore) print $0} }' blastout.txt


Keep only top bit scores in blast hits (5 less than the top):

    awk '{ if(!x[$1]++) {print $0; bitscore=($14-6)} else { if($14>bitscore) print $0} }' blastout.txt


Split a multi-FASTA file into individual FASTA files:

    awk '/^>/{s=++d".fa"} {print > s}' multi.fa

Output sequence name and its length for every sequence within a fasta file:

    cat file.fa | awk '$0 ~ ">" {print c; c=0;printf substr($0,2,100) "\t"; } $0 !~ ">" {c+=length($0);} END { print c; }'

Convert a FASTQ file to FASTA:

    sed -n '1~4s/^@/>/p;2~4p' file.fq > file.fa

Extract every 4th line starting at the second line (extract the sequence from FASTQ file):

    sed -n '2~4p' file.fq

Calculate the sum of column 2 and 3 and put it at the end of a row:

    awk '{print $0,$2+$3}' input.txt

Calculate the mean length of reads in a fastq file:

    awk 'NR%4==2{sum+=length($0)}END{print sum/(NR/4)}' input.fastq

Convert a VCF file to a BED file

    sed -e 's/chr//' file.vcf | awk '{OFS="\t"; if (!/^#/){print $1,$2-1,$2,$4"/"$5,"+"}}'
  
Readable VCF file on command line

    grep -v "^##" $1 | awk 'BEGIN{OFS="\t"} {split($8, a, ";"); print $1,$2,$4,$5,$6,a[1],$9,$10}'
    
Remove 'chr' in VCF file

    awk '{gsub(/^chr/,""); print}' your.vcf > no_chr.vcf

Will add the 'chr' to the VCF file that is without 'chr'.

    awk '{if($0 !~ /^#/) print "chr"$0; else print $0}' no_chr.vcf > with_chr.vcf

Get unique sequences/reads from SAM file (slow, fast)

    cut -f10 alignment.sam | sort -u | wc -l
    awk '{r[$10]++;}END{for(i in r)j++; print "number of species:", j;}' alignment.sam

Shuffle/Randomize read order in FASTQ file (shuf below can be replaced with sort -R). getline is used to put each 4-line fastq entry on a single line.

    awk '{OFS="\t"; getline seq; \
                getline sep; \
                getline qual; \
                print $0,seq,sep,qual}' reads.fq | \
    shuf | \
    awk '{OFS="\n"; print $1,$2,$3,$4}' \
    > reads.shuffled.fq

Select random read pairs from FASTQ file (somewhat mimics seqtk sample)

    paste f1.fastq f2.fastq |\ #merge the two fastqs
    awk '{ printf("%s",$0); n++; if(n%4==0) { printf("\n");} else { printf("\t\t");} }' |\ #merge by group of 4 lines
    shuf  |\ #shuffle
    head |\ #only 10 records
    sed 's/\t\t/\n/g' |\ #restore the delimiters
    awk '{print $1 > "file1.fastq"; print $2 > "file2.fatsq"}' #split in two files

Extract unmapped reads without header

     bioawk -c sam 'and($flag,4)' aln.sam.gz
     
Extract mapped reads with header

    bioawk -Hc sam '!and($flag,4)'
    
Reverse complement FASTA

    bioawk -c fastx '{print ">"$name;print revcomp($seq)}' seq.fa.gz

Create FASTA from SAM (uses revcomp if FLAG & 16)
    
    samtools view aln.bam | \
     bioawk -c sam '{s=$seq; if(and($flag, 16)) {s=revcomp($seq)} print ">"$qname"\n"s}'

Print the genotypes of sample foo and bar from a VCF

    grep -v ^## in.vcf | bioawk -tc hdr '{print $foo,$bar}'
     
Get the %GC from FASTA

    bioawk -c fastx '{ print ">"$name; print gc($seq) }' seq.fa.gz
    
Get the mean Phred quality score from FASTQ:

    bioawk -c fastx '{ print ">"$name; print meanqual($qual) }' seq.fq.gz
    
Take column name from the first line (where "age" appears in the first line of input.txt):

    bioawk -c header '{ print $age }' input.txt

## sort, uniq, cut, join, grep

[[back to top](#contents)]

Number each line in file.txt:

    cat -n file.txt

Count the number of unique lines in file.txt

    cat file.txt | sort -u | wc -l


Find lines shared by 2 files (assumes lines within file1 and file2 are unique; pipe to `wd -l` to count the _number_ of lines shared):

    sort file1 file2 | uniq -d

    # Safer
    sort -u file1 > a
    sort -u file2 > b
    sort a b | uniq -d

    # Use comm
    comm -12 file1 file2

Sort numerically (with logs) (g) by column (k) 9:

    sort -gk9 file.txt

Group rows by chromosome and sort by position and increase memory buffer...GTF file

    sort -k1,1 -k4,4n --parallel 4 Mus_musculus.GRCm38.75_chr1_random.gtf
    sort -k1,1 -k4,4n -S2G Mus_musculus.GRCm38.75_chr1_random.gtf


Find the most common strings in column 2:

    cut -f2 file.txt | sort | uniq -c | sort -k1nr | head


Exclude a column with cut or awk (e.g., all but the 5th field in a tab-delimited file, 1 and 3rd column for awk for csv file):

    cut -f5 --complement
    awk -F, '{$1=$3=""}1' file


Pick 10 random lines from a file:

    shuf file.txt | head -n 10


Print all possible 3mer DNA sequence combinations:

    echo {A,C,T,G}{A,C,T,G}{A,C,T,G}


Join on unpairable lines, out tab separated. Specify file in -a to have unpairable entries

    join -1 1 -2 1 -a 1 -t $'\t' example_sorted.bed example_lengths_alt.txt


Remove blank lines from a file using grep and save output to new file:

    grep . filename > newfilename


Find files containing text (`-l` outputs only the file names, `-i` ignores the case `-r` descends into subdirectories)

    grep -lir "some text" *


Find files containing text (`-h` no filename, `-i` ignores the case `-B2` print 2 lines before context)

    grep -hiB2 "Daniele" /home/*.txt


Untangle an interleaved paired-end FASTQ file. If a FASTQ file has paired-end reads intermingled, and you want to separate them into separate /1 and /2 files, and assuming the /1 reads precede the /2 reads:

    cat interleaved.fq |paste - - - - - - - - | tee >(cut -f 1-4 | tr "\t" "\n" > deinterleaved_1.fq) | cut -f 5-8 | tr "\t" "\n" > deinterleaved_2.fq

Take a fasta file with a bunch of short scaffolds, e.g., labeled `>Scaffold12345`, remove them, and write a new fasta without them:

    samtools faidx genome.fa && grep -v Scaffold genome.fa.fai | cut -f1 | xargs -n1 samtools faidx genome.fa > genome.noscaffolds.fa

Display hidden control characters:

    python -c "f = open('file.txt', 'r'); f.seek(0); file = f.readlines(); print file" 


## find, xargs, exec and GNU parallel

[[back to top](#contents)]


*Download GNU parallel at <https://www.gnu.org/software/parallel/>.*


Search for .bam files anywhere in the current directory recursively:

    find . -name "*.bam"

Delete all .bam files using xargs (Irreversible: use with caution! Confirm list BEFORE deleting, default xargs passes ALL arguments to ONE program, see below for alternative):

    find . -name "*.bam" | xargs rm

Safe delettion all .fastq files using xargs (here rm is called seperately for each argument specified by the -n option). Inspect list then run the script.

    find . -name "*-temp.fastq" | xargs -n 1 echo "rm -i" > delete-temp.sh
    cat delete-temp.sh
    bash delete-temp.sh

Delete all .txt files (which have spaces in file names) using xargs

    find . -name "samples [AB].txt" -print0 | xargs -0 rm

Delete all .fastq files using exec

    find . -name "*-temp.fastq" -exec rm -i {} \;

Rename all .txt files to .bak (backup *.txt before doing something else to them, for example):

    find . -name "*.txt" | sed "s/\.txt$//" | xargs -I {} echo mv {}.txt {}.bak | sh

Run processes simultaneously (parallelizing) using xargs with -P option. 

    find . -name "*.fastq" | xargs basename -s ".fastq" | \
    xargs -P 6 -I{} fastq_stat --in {}.fastq --out ../summaries/{}.txt

Replace a String in Multiple Files (with backup)

    find /path -type f -exec sed -i.bak 's/string/replacement/g' {} \;
    
Chastity filter raw Illumina data (grep reads containing `:N:`, append (-A) the three lines after the match containing the sequence and quality info, and write a new filtered fastq file):

    find *fq | parallel "cat {} | grep -A 3 '^@.*[^:]*:N:[^:]*:' | grep -v '^\-\-$' > {}.filt.fq"

Run FASTQC in parallel 12 jobs at a time:

    find *.fq | parallel -j 12 "fastqc {} --outdir ."

Index your bam files in parallel, but only echo the commands (`--dry-run`) rather than actually running them:

    find *.bam | parallel --dry-run 'samtools index {}'

Find directories older than 4 months and owned by specific user

    find /dir1/*/dir3/ -maxdepth 1 -type d -mtime +120 -user bobiger

Find large files (e.g., >500M):

    find . -type f -size +500M

When using GNU parallel, always start with these parameters, -j1 (one job at a time), -k (maintain order), --dry-run (see code before submitting)

    seq 10 | parallel -j1 -k --dry-run "echo {}"

    
Parallelizing BLAT,  start a blat process for each processor and distribute foo.fa to these in 1 MB blocks

    cat foo.fa | parallel --round-robin --pipe --recstart '>' 'blat -noHead genome.fa stdin >(cat) >&2' >foo.psl
    
Blast on multiple machines.
Assume you have a 1 GB fasta file that you want blast, GNU Parallel can then split the fasta file into 100 KB chunks and run 1 jobs per CPU core:

    cat 1gb.fasta | parallel --block 100k --recstart '>' --pipe blastp -evalue 0.01 -outfmt 6 -db db.fa -query - > results
    
If you have access to the local machine, server1 and server2, GNU Parallel can distribute the jobs to each of the servers. It will automatically detect how many CPU cores are on each of the servers:

    cat 1gb.fasta | parallel -S :,server1,server2 --block 100k --recstart '>' --pipe blastp -evalue 0.01 -outfmt 6 -db db.fa -query - > result
    
Run bigWigToWig for each chromosome
If you have one file per chomosome it is easy to parallelize processing each file. Here we do bigWigToWig for chromosome 1..19 + X Y M. These will run in parallel but only one job per CPU core. The {} will be substituted with arguments following the separator ':::'.

    parallel bigWigToWig -chrom=chr{} wgEncodeCrgMapabilityAlign36mer_mm9.bigWig mm9_36mer_chr{}.map ::: {1..19} X Y M
    
Running composed commands
GNU Parallel is not limited to running a single command. It can run a composed command. Here is now you process multiple FASTA files using Biopieces (which uses pipes to communicate):

    parallel 'read_fasta -i {} | extract_seq -l 5 | write_fasta -o {.}_trim.fna -x' ::: *.fna
 
Running experiments
Experiments often have several parameters where every combination should be tested. Assume we have a program called experiment that takes 3 arguments: --age --sex --chr:
    
    experiment --age 18 --sex M --chr 22
    
Now we want to run experiment for every combination of ages 1..80, sex M/F, chr 1..22+XY:

    parallel experiment --age {1} --sex {2} --chr {3} ::: {1..80} ::: M F ::: {1..22} X Y
    
To save the output in different files you could do:
    
    parallel experiment --age {1} --sex {2} --chr {3} '>' output.{1}.{2}.{3} ::: {1..80} ::: M F ::: {1..22} X Y
    
But GNU Parallel can structure the output into directories so you avoid having thousands of output files in a single dir.This will create files like outputdir/1/80/2/M/3/X/stdout containing the standard output of the job.

    parallel --results outputdir experiment --age {1} --sex {2} --chr {3} ::: {1..80} ::: M F ::: {1..22} X Y
    
If you want the output in a CSV/TSV-file that you can read into R or LibreOffice Calc, simply point --result to a file ending in .csv/.tsv. It will deal correctly with newlines in the output, so they will be read as newlines in R or LibreOffice Calc.

    parallel --result output.tsv --header : experiment --age {AGE} --sex {SEX} --chr {CHR} ::: AGE {1..80} ::: SEX M F ::: CHR {1..22} X Y
    
If one of your parameters take on many different values, these can be read from a file using '::::'

    echo AGE > age_file
    seq 1 80 >> age_file
    parallel --results outputdir --header : experiment --age {AGE} --sex {SEX} --chr {CHR} :::: age_file ::: SEX M F ::: CHR {1..22} X Y
    
With --shuf GNU Parallel will shuffle the experiments and run them all, but in random order:

    parallel --shuf --results outputdir --header : experiment --age {AGE} --sex {SEX} --chr {CHR} :::: age_file ::: SEX M F ::: CHR {1..22} X Y

## seqtk

[[back to top](#contents)]

*Download seqtk at <https://github.com/lh3/seqtk>. Seqtk is a fast and lightweight tool for processing sequences in the FASTA or FASTQ format. It seamlessly parses both FASTA and FASTQ files which can also be optionally compressed by gzip.*


Convert FASTQ to FASTA:

    seqtk seq -a in.fq.gz > out.fa


Convert ILLUMINA 1.3+ FASTQ to FASTA and mask bases with quality lower than 20 to lowercases (the 1st command line) or to `N` (the 2nd):

    seqtk seq -aQ64 -q20 in.fq > out.fa
    seqtk seq -aQ64 -q20 -n N in.fq > out.fa


Fold long FASTA/Q lines and remove FASTA/Q comments:

    seqtk seq -Cl60 in.fa > out.fa


Convert multi-line FASTQ to 4-line FASTQ:

    seqtk seq -l0 in.fq > out.fq


Reverse complement FASTA/Q:

    seqtk seq -r in.fq > out.fq


Extract sequences with names in file `name.lst`, one sequence name per line:

    seqtk subseq in.fq name.lst > out.fq


Extract sequences in regions contained in file `reg.bed`:

    seqtk subseq in.fa reg.bed > out.fa


Mask regions in `reg.bed` to lowercases:

    seqtk seq -M reg.bed in.fa > out.fa


Subsample 10000 read pairs from two large paired FASTQ files (remember to use the same random seed to keep pairing):

    seqtk sample -s100 read1.fq 10000 > sub1.fq
    seqtk sample -s100 read2.fq 10000 > sub2.fq


Trim low-quality bases from both ends using the Phred algorithm:

    seqtk trimfq in.fq > out.fq


Trim 5bp from the left end of each read and 10bp from the right end:

    seqtk trimfq -b 5 -e 10 in.fa > out.fa


Untangle an interleaved paired-end FASTQ file. If a FASTQ file has paired-end reads intermingled, and you want to separate them into separate /1 and /2 files, and assuming the /1 reads precede the /2 reads:

    seqtk seq -l0 -1 interleaved.fq > deinterleaved_1.fq
    seqtk seq -l0 -2 interleaved.fq > deinterleaved_2.fq




## GFF3 Annotations

[[back to top](#contents)]


Print all sequences annotated in a GFF3 file.

    cut -s -f 1,9 yourannots.gff3 | grep $'\t' | cut -f 1 | sort | uniq


Determine all feature types annotated in a GFF3 file.

    grep -v '^#' yourannots.gff3 | cut -s -f 3 | sort | uniq


Determine the number of genes annotated in a GFF3 file.

    grep -c $'\tgene\t' yourannots.gff3


Extract all gene IDs from a GFF3 file.

    grep $'\tgene\t' yourannots.gff3 | perl -ne '/ID=([^;]+)/ and printf("%s\n", $1)'


Print length of each gene in a GFF3 file.

    grep $'\tgene\t' yourannots.gff3 | cut -s -f 4,5 | perl -ne '@v = split(/\t/); printf("%d\n", $v[1] - $v[0] + 1)'


FASTA header lines to GFF format (assuming the length is in the header as an appended "\_length" as in [Velvet](http://www.ebi.ac.uk/~zerbino/velvet/) assembled transcripts):

    grep '>' file.fasta | awk -F "_" 'BEGIN{i=1; print "##gff-version 3"}{ print $0"\t BLAT\tEXON\t1\t"$10"\t95\t+\t.\tgene_id="$0";transcript_id=Transcript_"i;i++ }' > file.gff




## Other generally useful aliases for your .bashrc

[[back to top](#contents)]


Get a prompt that looks like `user@hostname:/full/path/cwd/:$ `

    export PS1="\u@\h:\w\\$ "


Never type `cd ../../..` again (or use [autojump](https://github.com/joelthelion/autojump), which enables you to navigate the filesystem faster):

    alias ..='cd ..'
    alias ...='cd ../../'
    alias ....='cd ../../../'
    alias .....='cd ../../../../'
    alias ......='cd ../../../../../'

Browse 'up' and 'down'

    alias u='clear; cd ../; pwd; ls -lhGgo'
    alias d='clear; cd -; ls -lhGgo'


Ask before removing or overwriting files:

    alias mv="mv -i"
    alias cp="cp -i"  
    alias rm="rm -i"


My favorite `ls` aliases:

    alias ls="ls -1p --color=auto"
    alias l="ls -lhGgo"
    alias ll="ls -lh"
    alias la="ls -lhGgoA"
    alias lt="ls -lhGgotr"
    alias lS="ls -lhGgoSr"
    alias l.="ls -lhGgod .*"
    alias lhead="ls -lhGgo | head"
    alias ltail="ls -lhGgo | tail"
    alias lmore='ls -lhGgo | more'


Use `cut` on space- or comma- delimited files:

    alias cuts="cut -d \" \""
    alias cutc="cut -d \",\""


Pack and unpack tar.gz files:

    alias tarup="tar -zcvf"
    alias tardown="tar -zxvf"


Or use a generalized `extract` function:

    # as suggested by Mendel Cooper in "Advanced Bash Scripting Guide"
    extract () {
       if [ -f $1 ] ; then
           case $1 in
            *.tar.bz2)      tar xvjf $1 ;;
            *.tar.gz)       tar xvzf $1 ;;
            *.tar.xz)       tar Jxvf $1 ;;
            *.bz2)          bunzip2 $1 ;;
            *.rar)          unrar x $1 ;;
            *.gz)           gunzip $1 ;;
            *.tar)          tar xvf $1 ;;
            *.tbz2)         tar xvjf $1 ;;
            *.tgz)          tar xvzf $1 ;;
            *.zip)          unzip $1 ;;
            *.Z)            uncompress $1 ;;
            *.7z)           7z x $1 ;;
            *)              echo "don't know how to extract '$1'..." ;;
           esac
       else
           echo "'$1' is not a valid file!"
       fi
    }



Use `mcd` to create a directory and `cd` to it simultaneously:

    function mcd { mkdir -p "$1" && cd "$1";}


Go up to the parent directory and list it's contents:

    alias u="cd ..;ls"


Make grep pretty:

    alias grep="grep --color=auto"

Refresh your `.bashrc`:

    alias refresh="source ~/.bashrc"

Edit your `.bashrc`:

    alias eb="vi ~/.bashrc"

Common typos:

    alias mf="mv -i"
    alias mroe="more"
    alias c='clear'
    alias emacs='vim'

Show your `$PATH` in a prettier format:

    alias showpath='echo $PATH | tr ":" "\n" | nl'

Use [pandoc](http://johnmacfarlane.net/pandoc/) to convert a markdown file to PDF:

    # USAGE: mdpdf document.md document.md.pdf
    alias mdpdf="pandoc -s -V geometry:margin=1in -V documentclass:article -V fontsize=12pt"


Find text in any file (`ft "mytext" *.txt`):

    function ft { find . -name "$2" -exec grep -il "$1" {} \;; }

Transpose file

    function transpose()
    {
    awk '
    { 
        for (i=1; i<=NF; i++)  {
            a[NR,i] = $i
        }
    }
    NF>p { p = NF }
    END {    
    for(j=1; j<=p; j++) {
        str=a[1,j]
        for(i=2; i<=NR; i++){
            str=str" "a[i,j];
        }
        print str
        }
    }' $1
    }
    
Creates an archive (*.tar.gz) from given directory.

    function maketar() { tar cvzf "${1%%/}.tar.gz"  "${1%%/}/"; }

Create a ZIP archive of a file or folder.

    function makezip() { zip -r "${1%%/}.zip" "$1" ; }

Make your directories and files access rights sane.

    function sanitize() { chmod -R u=rwX,g=rX,o= "$@" ;}

get header from tsv or csv files
    
    function header() {
        sed -e '1s/\t\|,/\n/g;q' $1
    }

reverse complement a DNA Sequence
    
    function revcomp {
        echo $1 | tr "[ATGCatgcNn]" "[TACGtacgNn]" | rev
    }

    

## Etc
[[back to top](#contents)]


Colorized grep — viewing the entire file with highlighted matches and piped into less

    grep --color=always -E 'pattern|$' file | less -R

"Zip a directory":

    tar -zcvf archive.tar.gz directory/ 
    tar -cv directory | gzip > archive.tar.gz (same as above)
    
Decompress:

    tar -zxvf archive.tar.gz
    gunzip < archive.tar.gz | tar -xv (same as above)
    
gzip/gunzip (by default will compress/decompress file in space)

    gzip in.fastq
    gunzip in.fastq.gz
    
gzip/gunzip (keep original file)

    gzip -c in.fastq > in.fastq.gz
    gunzip -c in.fastq.gz > duplicate_in.fastq

Run the last command as root:

    sudo !!

Quickly backup or copy a file:
    
    cp filename{,.bak}

Place the argument of the most recent command on the shell:

    'ALT+.' or '<ESC> .'

Type partial command, kill this command, check something you forgot, yank the command, resume typing:

    <CTRL+u> [...] <CTRL+y>
    
Terminal/Command line shortcuts

    <CTRL+k>    # cuts to the end of the line
    <CTRL+u>    # cuts to the start of the line
    <CTRL+y>    # pastes whatever you just cut
    <CTRL+w>    # delete the last word
    <CTRL+t>    # exchange two adjacent characters quickly
    <ALT+t>     # exchange two adjacent words quickly
    <ALT+u||l>  # upper/lower case word
    <ALT+r>     # undo all changes to the line
    <CTRL+x> <CTRL+u>   # Incremental undo
    <CTRL+x> <CTRL+e>   # Open the current command in a text editor quickly, 'export EDITOR=vim' for bashrc
    <ALT+.>     # inserts the argument of the last successful command
    <CTRL+z>    # Send job/app to background
    fg          # Bring job/app back

Jump to a directory, execute a command, and jump back to the current directory:

    (cd /tmp && ls)

Stopwatch (`Enter` or `ctrl-d` to stop):

    time read

Create a script of the last executed command:

    echo "!!" > foo.sh

Reuse _all_ parameter of the previous command line:

    !*

List or delete all files in a folder that don't match a certain file extension (e.g., list things that are _not_ compressed; remove anything that is _not_ a `.foo` or `.bar` file):

    ls !(*.gz)
    rm !(*.foo|*.bar)

Insert the last command without the last argument:

    !:- <new_last_argument>

Rapidly invoke an editor to write a long, complex, or tricky command:

    fc


Terminate a frozen SSH session (enter a new line, type the `~` key then the `.` key):

    [ENTER]~.


See non-ASCII characters 


    LC_CTYPE=C grep --color='auto' -P "[\x80-\xFF]" improper.fa
    alias nonascii="LC_CTYPE=C grep --color='auto' -n -P '[\x80-\xFF]'"

    file improper.fa
    hexdump -c improper.fa
    
Pretend to be busy

    cat /dev/urandom | hexdump -C | grep "ca fe"
    echo "You can simulate on-screen typing just like in the movies" | pv -qL 10
    https://github.com/kyle8998/Sudo-Productivity
    
Use tee to process a pipe with two or more processes
    
    echo "tee can split a pipe in two" | tee >(rev) >(tr ' ' '_')

Reverse the order of a text (concatenate and print files in reverse, opposite of cat)

    tac text.txt
    
Get total size of files in a list (to get file size for each remove grep command)

    cat file | awk '{printf "%s\0", $1}' | du -hsc --files0-from - | grep -i total
    find . -print0 | du -hsc --files0-from - | grep -i total
    
rsync command with progress bar. So if you have 42 files in /tmp/software and you would like to copy them to /nas10, enter:

    rsync -vrltD --stats --human-readable /tmp/software /nas10 | pv -lep -s 42

PBS commands on cluster,HPF

    qsub              #submit a job, see man qsub
    qdel -p jobid     #will force purge the job if it is not killed by qdel 
    qstat             #list information about queues and jobs
    showq             #calculated guess which job will run next
    showq -r -u user -n -c          #completed jobs in the last 3 days
    xpbs              #GUI to PBS commands
    qstat -q          #list all queues on system
    qstat -Q          #list queue limits for all queues
    qstat -a          #list all jobs on system
    qstat -s          #list all jobs with status comments
    qstat -r          #list all running jobs
    qstat -f1 jobid    #list full information known about jobid
    qstat -Qf queueid #list all information known about queueid
    qstat -Qf         #list all infomation about queues
    qstat -B          #list summary information about the PBS server
    qstat -iu userid  #get info for queued jobs of userid
    qstat -u userid   #get info for all the jobs of userid
    qstat -n -1 jobid #will list nodes on which jobid is running in one line
    checkjob jobid    #will list job details
    qselect -u user | xargs qdel    # delete all jobs for user
    ldapsearch -x | less    #show info on all users on cluster
