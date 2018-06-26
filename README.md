# Bioinformatics one-liners

[![DOI](https://zenodo.org/badge/3882/stephenturner/oneliners.svg)](https://zenodo.org/badge/latestdoi/3882/stephenturner/oneliners)

Useful bash one-liners useful for bioinformatics (and [some, more generally useful](#etc)).  


## Contents

- [Sources](#sources)
- [Basic perl](#basic-perl)
- [Basic awk & sed](#basic-awk--sed)
- [awk & sed for bioinformatics](#awk--sed-for-bioinformatics)
- [sort, uniq, cut, etc.](#sort-uniq-cut-etc)
- [find, xargs, and GNU parallel](#find-xargs-and-gnu-parallel)
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


## Basic perl

[[back to top](#contents)]

Substitution conditional. -p argument ensures that the code is executed on every line of input and
that the line is printed after execution The -i argument ensures that file is edited in-place, .bak ensures there's a backup file before change is made. -e means execute code

    perl -pi.bak -e 's/you/me/g if /we/' file


## Basic awk & sed

[[back to top](#contents)]


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


Display a Block of Text between Two Strings

    awk '/start-pattern/,/stop-pattern/' file.txt
    sed -n '/start-pattern/,/stop-pattern/p' file.txt

Remove duplicate entries in a file without sorting

    awk '!x[$0]++' <file>


Replace/convert white space to tab

    expand (cmd line)
    unexpand (cmd line)
    awk -v OFS="\t" '$1=$1' file1
    sed 's/[:blank:]+/,/g' thefile.txt > the_modified_copy.txt
    tr ' ' \\t < someFile > someFile
  

Delete a Block of Text between Two Strings (inclusive)

    sed '/start-pattern/,/stop-pattern/d' file

Replace all occurances of `foo` with `bar` in file.txt:

    sed 's/foo/bar/g' file.txt


Trim leading whitespaces and tabulations in file.txt:

    sed 's/^[ \t]*//' file.txt


Trim trailing whitespaces and tabulations in file.txt:

    sed 's/[ \t]*$//' file.txt


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


## awk & sed for bioinformatics

[[back to top](#contents)]


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

Print everything except the first line

    awk 'NR>1' input.txt

Print rows 20-80:

    awk 'NR>=20&&NR<=80' input.txt

Calculate the sum of column 2 and 3 and put it at the end of a row:

    awk '{print $0,$2+$3}' input.txt

Calculate the mean length of reads in a fastq file:

    awk 'NR%4==2{sum+=length($0)}END{print sum/(NR/4)}' input.fastq

Convert a VCF file to a BED file

    sed -e 's/chr//' file.vcf | awk '{OFS="\t"; if (!/^#/){print $1,$2-1,$2,$4"/"$5,"+"}}'

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

Remove 'chr' in VCF file

    awk '{gsub(/^chr/,""); print}' your.vcf > no_chr.vcf

Will add the 'chr' to the VCF file that is without 'chr'.

    awk '{if($0 !~ /^#/) print "chr"$0; else print $0}' no_chr.vcf > with_chr.vcf

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


Join on unpairable lines. Specify file in -a to have unpairable entries

    join -1 1 -2 1 -a 1 example_sorted.bed example_lengths_alt.txt


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


Delete all .bam files using xargs (Irreversible: use with caution! Confirm list BEFORE deleting):

    find . -name "*.bam" | xargs rm


Delete all .txt files (which have spaces in file names) using xargs

    find . -name "samples [AB].txt" -print0 | xargs -0 rm


Delete all .fastq files using exec


    find . -name "*-temp.fastq" -exec rm -i {} \;


Rename all .txt files to .bak (backup *.txt before doing something else to them, for example):

    find . -name "*.txt" | sed "s/\.txt$//" | xargs -i echo mv {}.txt {}.bak | sh

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
    
Use tee to process a pipe with two or more processes
    
    echo "tee can split a pipe in two" | tee >(rev) >(tr ' ' '_')
