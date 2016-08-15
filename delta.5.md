% DELTA(5) 3.0 | Bioinformatics Sequence Alignment Formats
% 
% October 2015

# NAME

delta - MUMmer delta format

# DESCRIPTION

The **delta** file is an encoded representation of the all-vs-all alignment
between the input sequences to either the **nucmer**(1) or **promer**(1)
pipeline. It is the primary output of these alignment scripts and there are
various utilities in the MUMmer package that are designed to take the **delta**
file as input, and output some human-readable information to the user. Also, the
**delta-filter**(1) utility is designed to manipulate these files and select
desired alignments. The primary function of the delta file is to catalog the
coordinates of each alignment and note the distance between insertions and
deletions contained in these alignments. By only storing the location of each
indel as an offset, disk space is efficiently utilized, and a potentially
enormous alignment can be stored in a relatively small space. The first line
lists the two original input files separated by a space, while the second line
specifies the alignment data type, either "NUCMER" or "PROMER". Every grouping
of alignments have a unique header specifying the two aligning sequences'
labels and their total length. Only sequences with shared alignments will have a
header; therefore, there can be no empty headers (i.e. those that have no
alignments following them). An example header might look like:

~~~
>tagA1 tagB1 500 20000000
~~~

Following this sequence header is the alignment data. Each alignment following
also has a header that describes the coordinates of the alignment and some
error information. These coordinates are inclusive and reference the forward
strand of the DNA sequence, regardless of the alignment type (DNA or amino
acid). Thus, if the start coordinate is greater than the end coordinate, the
alignment is on the reverse strand. The four coordinates are the start and end
in the reference and the start and end in the query respectively. The three
digits following the location coordinates are the number of errors
(non-identities + indels), similarity errors (non-positive match scores), and
stop codons (does not apply to DNA alignments, will be "0"). An example header
might look like:

~~~
2631 3401 2464 3234 15 15 2
~~~

Notice that the start coordinate points to the first base in the first codon,
and the end coordinate points to the last base in the last codon. Therefore
making `(end - start + 1) % 3 = 0`. This makes determining the frame of the
amino acid alignment a simple matter of determining the reading frame of the
start coordinate for the reference and query. Obviously, these calculations are
not necessary when dealing with vanilla DNA alignments.

Each of these alignment headers is followed by a string of signed digits, one
per line, with the final line before the next header equaling 0 (zero). Each
digit represents the distance to the next insertion in the reference (positive
int) or deletion in the reference (negative int), as measured in DNA bases OR
amino acids depending on the alignment data type. For example, with the PROMER
data type, the delta sequence (1, -3, 4, 0) would represent an insertion at
positions 1 and 7 in the translated reference sequence and an insertion at
position 3 in the translated query sequence. Or with letters:

~~~
A = ABCDACBDCAC$
B = BCCDACDCAC$
Delta = (1, -3, 4, 0)
A = ABC.DACBDCAC$
B = .BCCDAC.DCAC$
~~~

Using this delta information, it is possible to re-generate the alignments
calculated by nucmer or promer as is done in the **show-coords**(1) program.
This allows various utilities to be crafted to process and analyze the
alignment data using a universal format. This also means the delta only needs
to be created once, yet it can be analyzed numerous times without ever having
to rerun the costly alignment algorithm.

# EXAMPLE
~~~
/home/username/reference.fasta /home/username/query.fasta
PROMER
>tagA1 tagB1 3000000 2000000
1667803 1667078 1641506 1640769 14 7 2
-145
-3
-1
-40
0
1667804 1667079 1641507 1640770 10 5 3
-146
-1
-1
-34
0
>tagA2 tagB4 4000 3000
2631 3401 2464 3234 4 0 0
0
2608 3402 2456 3235 10 5 0
7
1
1
1
1
0
(output continues ...)
~~~

# SEE ALSO
**nucmer**(1)
**promer**(1)

Section 5.3.1 of the MUMmer 3 manual, subsection "Output format", from which
this manual page was derived.\
http://mummer.sourceforge.net/manual/#nucmer
