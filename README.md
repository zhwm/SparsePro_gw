# SparsePro for genome-wide fine-mapping

Here we showcase how to use SparePro for genome-wide fine-mapping with GWAS summary statistics and functional information data. 

For more information about locus fine-mapping, please refer to [SparsePro](https://github.com/zhwm/SparsePro).

## Installation

SparsePro was developed under Python 3.9.7 environment but should be compatible with older versions of Python 3. The following Python modules are required:

* [numpy](http://www.numpy.org/) (version==1.21.3)
* [scipy](http://www.scipy.org/) (version==1.7.1)
* [pandas](https://pandas.pydata.org/getpandas.html) (version==1.3.4)

To install SparsePro:

```
git clone https://github.com/zhwm/SparsePro_gw.git
cd SparsePro_gw
pip install -r requirements.txt 
``` 

To test the installation and display basic usage:

```
$> python sparsepro_ukb.py -h
usage: sparsepro_ukb.py [-h] [--ukb UKB] --zdir ZDIR --LDdir LDDIR --N N --save SAVE --prefix PREFIX [--verbose] [--anno ANNO]
                        [--K K] [--gwp GWP] [--pthres PTHRES] [--sthres STHRES] [--aW AW] [--h2]

SparsePro Commands:

optional arguments:
  -h, --help       show this help message and exit
  --ukb UKB        genome-wide finemapping mode: path to LD lists
  --zdir ZDIR      path to zscores files
  --LDdir LDDIR    path to ld files
  --N N            GWAS sample size
  --save SAVE      path to save result
  --prefix PREFIX  prefix for result files
  --verbose        options for displaying more information
  --anno ANNO      path to annotation file
  --K K            largest number of effect
  --gwp GWP        p-value threshold for filtering blocks to be fine-mapped
  --pthres PTHRES  p value threshold for enrichment
  --sthres STHRES  level for representation of effect group
  --aW AW          significant enriched file
  --h2             use previous h2 file as zld file
```

## Data

To perform genome-wide fine-mapping, we need z-scores from GWAS summary statistics, LD matrices and optinally functional information data. 

1. **GWAS z-scores** file contains two columns: variant IDs and z-scores. An example file can be found at [C1_22.z](dat/C1_22.z)

2. **LD matrix** files contain Pearson correlation coefficient matrix. **Please make sure the REF/ALT alleles used in calculating LD are the same as the GWAS study!!** UK biobank LD information provided by PolyFun can be downloaded from [here](https://alkesgroup.broadinstitute.org/UKBB_LD/). We have provided the [matchss.py](matchss.py) script to match alleles used in raw GWAS summary statistics with alleles used in calculating these LD matrix indicated at [22.rsid](ukb/22.rsid). 

3. **Functional annotation** files with entries indicating annotation status for variants. An example can be find at [22.anno](ukb/22.anno).

4. **LD index** file contains a list of LD blocks to be fine-mapped. An example can be find at [22.lst](ukb/22.lst).

## Match GWAS summary statistics with UK Biobank LD reference

To obtain the GWAS z-scores for SparsePro, we use a raw GWAS summary statistics file [C1.fastGWA](dat/C1.fastGWAS) obtained from fastGWA. We match GWAS effective alleles with alleles used for LD calculation by flipping the sign of z-score when they are different.

```
python matchss.py --rss dat/C1.fastGWA --prefix C1 --save dat --idir ukb/ --CHR CHR --POS POS --A1 A1 --A2 A2 --BETA BETA --SE SE --ch 22
```

## Fine-map with GWAS summary statistics and estimate functional enrichment

After matching alleles, we can perform fine-mapping with z-scores and LD matrix downloaded from [PolyFun](https://alkesgroup.broadinstitute.org/UKBB_LD/).

```
python sparsepro_ukb.py --ukb ukb/22.lst --zdir dat/C1_22.z --N 353570 --save result_no --prefix C1 --verbose --LDdir path_to_ld_matrix --anno ukb/22.anno
```

## Fine-map with GWAS summary statistics and relevant functional annotation

We can also integrate relevant functional annotations with GWAS summary statstics to further prioritize causal variants.

```
python sparsepro_ukb.py --ukb ukb/22.lst --zdir dat/C1_22.z --N 353570 --save result_anno --prefix C1 --verbose --LDdir path_to_ld_matrix --anno ukb/22.anno --aW result_no/C1.W1e-05 
```

## Citations

If you use this software, please cite:

[Wenmin Zhang, Hamed Najafabadi, Yue Li. SparsePro: an efficient fine-mapping method integrating summary statistics and functional annotations. bioRxiv 2021.10.04.463133](https://doi.org/10.1101/2021.10.04.463133)
