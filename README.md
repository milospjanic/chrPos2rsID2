# chrStartEnd2rsID

chrStartEnd2rsID is a script to convert a list of genomics positions in a format: chrno \t start \t end \t etc. to SNP rsIDs. Script will append rsIDs as a column to an existing file. chrStartEnd2rsID is useful for quick conversion to SNP rsIDs for various downstream analysis and lookups.

**Usage**

This script will check if the working folder ~/chrStartEnd2rsID is present and if not it will create ~/chrStartEnd2rsID. Next, script will go into ~/chrStartEnd2rsID and check if dbSNP bed file for human genome hg19 is present or not, version 147 (the latest one present in the mySQL database currently), and in case it is not present it will download it using mySQL from snp147Common table of hg19 dataset on genome-mysql.cse.ucsc.edu.

**Note - Script chrStartEnd2rsID.sh can be placed anywhere as well as the input file, however output will be in ~/chrStartEnd2rsID**

Your input file should be formatted as:

<pre>
chr	start	end	TF_indiv_accB	ref	alt	cA	cC	cG	cT	p.binomial	p.betabinomial
chr1	91604	91605	SA1_NA19099_accB	C	T	0	45	0	0	5.68434188608e-14	0.000376021311924829
chr1	713976	713977	POL2_NA18951_accB	C	T	0	32	0	27	0.602923201257	0.753317610684756
chr1	713976	713977	SA1_NA18951_accB	C	T	0	54	0	60	0.639575140949	0.93769632200937
chr1	713978	713979	POL2_NA18951_accB	C	G	0	27	26	0	1	0.999999999999998
chr1	713978	713979	SA1_NA18951_accB	C	G	0	60	59	0	1	0.999999999999996
chr1	714018	714019	POL2_NA18505_accB	A	G	2	0	19	0	0.000221252441406	0.00311474712114779
chr1	714018	714019	POL2_NA19099_accB	A	G	2	0	19	0	0.000221252441406	0.00255918242712794
chr1	714018	714019	SA1_NA18505_accB	A	G	54	0	16	0	5.85395552939e-06	0.242465581502621
chr1	714018	714019	SA1_NA19099_accB	A	G	44	0	27	0	0.0568146779328	0.567664969947665
</pre>

Next an akw code will perform comparison of your file and dbSNP MySQL data using 3 different hash tables in awk. Script will output a file of your input SNPs with rsID appended to it, separated by tab and save it as $1.rsID, $1 being first parameter provided to the script that should be the file name containing SNPs.

Script preserves the header of the original file and adds 'rsID' as a first field and prepends it to the output file.

Output file will be placed in ~/chrStartEnd2rsID

MySQL download will produce a file snp147Common.bed, with 14,815,821 SNPs:
<pre>
head snp147Common.bed
chr1	10177	10177	rs367896724
chr1	10352	10352	rs555500075
chr1	11007	11008	rs575272151
chr1	11011	11012	rs544419019
chr1	13109	13110	rs540538026
chr1	13115	13116	rs62635286
chr1	13117	13118	rs62028691
chr1	13272	13273	rs531730856
chr1	13417	13417	rs777038595
chr1	14463	14464	rs546169444

mpjanic@zoran:~/rsID2Bed$ wc -l snp147Common.bed 
14815821 snp147Common.bed
</pre>

**Script will check if dbSNP file exists and if it is parsed into categories, and if not it will download it from mySQL and parse the file into insertions (same base pair coordinates), SNPs plus simple deletions (single base pair coordinates), and large deletions (more than 1 base pair difference in the coordinates), that will be proccessed with a separate code and at the end merged into a single output.**

<pre>
-rw-rw-r-- 1 mpjanic mpjanic 494M Dec 14 18:48 snp147Common.bed
-rw-rw-r-- 1 mpjanic mpjanic  24M Mar 29 12:41 snp147Common.bed.insertions
-rw-rw-r-- 1 mpjanic mpjanic 455M Mar 29 12:41 snp147Common.bed.snp.plus.simple.deletions
-rw-rw-r-- 1 mpjanic mpjanic  16M Mar 29 12:41 snp147Common.bed.large.deletions

mpjanic@zoran:~/chrPos2rsID$ head snp147Common.bed.insertions
chr1	10177	10177	rs367896724
chr1	10352	10352	rs555500075
chr1	13417	13417	rs777038595
chr1	15903	15903	rs557514207
chr1	54712	54712	rs568927205
chr1	91551	91551	rs375085441
chr1	249275	249275	rs200079338
chr1	255923	255923	rs199745078
chr1	363244	363244	rs572571697
chr1	604229	604229	rs556776674
mpjanic@zoran:~/chrPos2rsID$ head snp147Common.bed.snp.plus.simple.deletions
chr1	11007	11008	rs575272151
chr1	11011	11012	rs544419019
chr1	13109	13110	rs540538026
chr1	13115	13116	rs62635286
chr1	13117	13118	rs62028691
chr1	13272	13273	rs531730856
chr1	14463	14464	rs546169444
chr1	14598	14599	rs531646671
chr1	14603	14604	rs541940975
chr1	14672	14673	rs4690
mpjanic@zoran:~/chrPos2rsID$ head snp147Common.bed.large.deletions
chr1	17358	17361	rs749387668
chr1	63735	63738	rs201888535
chr1	66435	66437	rs560481224
chr1	82133	82135	rs550749506
chr1	129010	129013	rs377161483
chr1	267227	267230	rs374780253
chr1	532325	532327	rs577455319
chr1	612688	612691	rs201365517
chr1	691567	691571	rs566250387
chr1	701779	701783	rs201234755
</pre>

**Running**

To run the script type:
<pre>
chmod 775 chrStartEnd2rsID.sh 
./chrStartEnd2rsID.sh path/to/file
</pre>

**Prerequisites**

MySQL

**Example**

<pre> 
head SNP.file
chr	start	end	TF_indiv_accB	ref	alt	cA	cC	cG	cT	p.binomial	p.betabinomial
chr1	91604	91605	SA1_NA19099_accB	C	T	0	45	0	0	5.68434188608e-14	0.000376021311924829
chr1	713976	713977	POL2_NA18951_accB	C	T	0	32	0	27	0.602923201257	0.753317610684756
chr1	713976	713977	SA1_NA18951_accB	C	T	0	54	0	60	0.639575140949	0.93769632200937
chr1	713978	713979	POL2_NA18951_accB	C	G	0	27	26	0	1	0.999999999999998
chr1	713978	713979	SA1_NA18951_accB	C	G	0	60	59	0	1	0.999999999999996
chr1	714018	714019	POL2_NA18505_accB	A	G	2	0	19	0	0.000221252441406	0.00311474712114779
chr1	714018	714019	POL2_NA19099_accB	A	G	2	0	19	0	0.000221252441406	0.00255918242712794
chr1	714018	714019	SA1_NA18505_accB	A	G	54	0	16	0	5.85395552939e-06	0.242465581502621
chr1	714018	714019	SA1_NA19099_accB	A	G	44	0	27	0	0.0568146779328	0.567664969947665

./chrStartEnd2rsID.sh SNP.file

head SNP.file.rsID
rsID  chr start   end TF_indiv_accB   ref alt cA  cC  cG  cT  p.binomial  p.betabinomial
rs74512038	chr1	713976	713977	POL2_NA18951_accB	C	T	0	32	0	27	0.602923201257	0.753317610684756
rs74512038	chr1	713976	713977	SA1_NA18951_accB	C	T	0	54	0	60	0.639575140949	0.93769632200937
rs114983708	chr1	714018	714019	POL2_NA18505_accB	A	G	2	0	19	0	0.000221252441406	0.00311474712114779
rs114983708	chr1	714018	714019	POL2_NA19099_accB	A	G	2	0	19	0	0.000221252441406	0.00255918242712794
rs114983708	chr1	714018	714019	SA1_NA18505_accB	A	G	54	0	16	0	5.85395552939e-06	0.242465581502621
rs114983708	chr1	714018	714019	SA1_NA19099_accB	A	G	44	0	27	0	0.0568146779328	0.567664969947665
rs12028261	chr1	714426	714427	SA1_NA19099_accB	G	A	18	0	9	0	0.122078120708	0.481887495592435
rs12095200	chr1	762484	762485	RPB2_NA11894_accB	C	A	0	11	0	0	0.0009765625	0.00456658602727794
rs3115848	chr1	762588	762589	RPB2_NA11830_accB	G	C	0	19	5	0	0.00661075115204	0.228966308052636
rs3115848	chr1	762588	762589	RPB2_NA11894_accB	G	C	0	13	1	0	0.0018310546875	0.0104005886741441
</pre>
