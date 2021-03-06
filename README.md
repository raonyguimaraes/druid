# DRUID
DRUID: deep relatedness inference utilizing identity by descent

Updated 23 Oct 2018
Version 1.02.1

If Refined IBD results differ by DRUID results by 3 or more degrees, you are likely seeing a bug in DRUID or you are dealing with an inbred pedigree.  
If the former, please report; if the latter, it may be beneficial to run DRUID on only select individuals.

Send any bug reports to monica.ramstetter@gmail.com



USAGE
-----

DRUID requires a minimum of three files:  
1) .map file (PLINK format) containing cM positions for SNPs (see https://www.cog-genomics.org/plink2/formats#map for formatting)  
2) .seg file containing pairwise IBD1/2 segment information: ind1 ind2 chr IBD1/2 IBD_segment_start IBD_segment_end (see below for creation)  
3) .ibd12 file containing pairwise proportion of genome shared IBD information: ind1 ind2 IBD1_proportion IBD2_proportion (see below for creation)




PROVIDING KNOWN RELATIONSHIP INFORMATION
----------------------------------------

DRUID can be provided first and second degree (specifically, half-sibling, grandparent-grandchild, avuncular) relationships to make use of via the flag `-f [file.faminfo]`.  
The .faminfo file should be space-delimited and formatted as follows:

ind1 ind2 [ind1's relationship to ind2]

'ind1's relationship to ind2' must be one of the following types:

FS: full-sibling  
P: parent  
C: child  
HS: half-sibling  
GP: grandparent  
GC: grandchild  
AU: aunt or uncle  
NN: niece or nephew

For example, if individual A is the aunt of individual B, we can provide this information to DRUID via a .faminfo file containing the following:

A B AU




RUNNING ONLY ON SPECIFIC INDIVIDUALS
------------------------------------

One may run DRUID on a subset of individuals in the dataset via the flag `-u [file.inds]`.  
If one wishes to analyze only individuals A,B, and K, 'file.inds' should appear as follows:

A  
B  
K





RUNNING DRUID CONSERVATIVE
--------------------------

Some datasets may have complex relationships or inbred individuals.  
While we recommend to be weary of results from such datasets, one may run a conservative version of DRUID (as described in the paper) via the flag `-C 1`.






.seg AND .ibd12 FILES CREATION
------------------------------

DRUID requires as input pairwise IBD segment information. Although one could use IBD information generated by any pairwise IBD segment detection method (assuming the files are properly formated), we recommend using Refined IBD (Beagle 4.1: https://faculty.washington.edu/browning/beagle/beagle.html).  
The authors of Beagle 4.1 recommend running Refined IBD three time with different random seeds. 

We have a script to merge IBD information from the three separate Refined IBD runs and extract pairwise IBD1/2 information as well as pairwise IBD1/2 segment information.  
This script is available at:  
http://github.com/MonicaRamstetter/bakeoff. 

To obtain the files necessary for running DRUID, one should run the getIBD.py script as follows:

`python getIBD.py -f [file1.ibd] [file2.ibd] [file3.ibd] -m [mapfile.map] -t 1 -s 1 -o [output_prefix]`

where 'file1.ibd', 'file2.ibd', and 'file3.ibd' are the three .ibd files output by Refined IBD, 'mapfile.map' is a PLINK formatted map file containing genetic position information which corresponds to the VCF file input to Refined IBD, `-t 1` ensures pairwise proportions of genome shared IBD are output, and `-s 1` ensures segment information is output.



DRUID OUTPUT
------------

Four columns:

ind1  
ind2  
DRUID (degree of relatedness/relationship inferred by DRUID)  
Refined IBD (degree of relatedness inferred by Refined IBD; can equal 1 through 11)

There is currently a fifth column, "Method", to help find potential bugs. If a pair's "Method" entry is empty, it indicates the pair's inference is the result of the ancestral IBD sharing profile reconstruction OR is Refined IBD's result in the case when no siblings are available; if a pair's "Method" entry equals "graph", the close relative graph information was used to infer the degree/type of relatedness.





