[![R](https://github.com/wenjie1991/scMitoMut/actions/workflows/r.yml/badge.svg)](https://github.com/wenjie1991/scMitoMut/actions/workflows/r.yml)

# The {scMitoMut} package

<img src="./CellBarcode_logo.png" width="200">

The [**{scMitoMut}**](https://github.com/wenjie1991/scMitoMut) is a R/Bioconductor Package for lineage informative mitochondrial mutation calling in Single-Cell sequencing.

The mitochondrial somatic mutations are promising lineage markers for single-cell sequencing data.
The {scMitoMut} package provides a comprehensive function to call the lineage informative mitochondrial mutations using beta-binomial model.

# Installation

## Install from bioconductor

```r
## Install `BiocManager` package to manage Bioconductor packages.
install.packages("BiocManager")
## Install `scMitoMut` from Bioconductor
BiocManager::install("scMitoMut")
```

## Install the devel version from GitHub or Bioconductor

```r
# install.packages("devtools")
devtools::install_github("wenjie1991/scMitoMut", build_vignettes = TRUE)
# or
BiocManager::install("scMitoMut", version = "devel")
```

# Vignette

A vignette can be found in [Bioconductor](https://www.bioconductor.org/packages/devel/bioc/vignettes/scMitoMut/inst/doc/Analysis_colon_cancer_dataset.html).

And the source code can be found [here](https://github.com/wenjie1991/scMitoMut/blob/main/vignettes/Analysis_colon_cancer_dataset.Rmd).

You can also access the vignette by the R command `browseVignettes('scMitoMut')` after installing the package.

# Mini Example

This is a simple example that demonstrates the main function of the package. It can be executed in less than 1 minute.

```r
library(scMitoMut)

# load the data
## Use the example data
f = system.file("extdata", "mini_dataset.tsv.gz", package = "scMitoMut")

## Load the data with parse_table function
f_h5 = parse_table(f, sep = "\t", h5_file = "./mut.h5")

## open the h5f file
x = open_h5_file(f_h5)

# run the model fit
# You can increase the cpu core to accelerate
# This step need some time, so the result will be kept in h5 file,
# you do not need to re-run this step, when you load the h5 file next time.
run_model_fit(x, mc.cores = 1)

# Filter the loci based on the model fit results
# The filter options will be keeped in the object by memory
# Next time you re-load the h5 file, the filter will be initiated as default
x = filter_loc(x, 
    min_cell = 5, 
    model = "bb", 
    p_threshold = 0.01, 
    p_adj_method = "fdr"
    )
x

# Set the cell annotation
f = system.file("extdata", "mini_dataset_cell_ann.csv", package = "scMitoMut")
cell_ann = read.csv(f, row.names=1)
# Prepare the color for cell annotation
colors = c(
    "Cancer Epi" = "#f28482",
    Blood = "#f6bd60")
ann_colors = list("SeuratCellTypes" = colors)

# plot the heatmap for p-value
plot_heatmap(x, type = "p", cell_ann = cell_ann, ann_colors = ann_colors, percent_interp = 0.2)

# plot the heatmap for allele frequency
plot_heatmap(x, type = "af", cell_ann = cell_ann, ann_colors = ann_colors, percent_interp = 0.2)

# check af~coverage for one loci
plot_af_coverage(x, "chrM.1227")
```

# Contribution

You are welcome to open an issue or make a pull request.
