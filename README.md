![CLICnet](clicnet.png)
CLICnet is a Python library for clustering cancer patients by survival rates,
based on their somatic mutation data. CLICnet is based on an RBM deep-learning model. 

## Overview 
This file describes a Python package that implements CLICnet, a Python library for clustering cancer patients by survival rates,
based on their somatic mutation data. CLICnet is based on an RBM deep-learning model. A web portal is also
available (http://clicnet.pythonanywhere.com/).

If you have any trouble installing or using CLICnet, please let us know by opening an issue on GitHub or emailing us 
(either ayal.gussow@gmail.com or noamaus@gmail.com). 

## Citation
[1]Ayal B Gussow,  Eugene V Koonin,  Noam Auslander. [Identification of combinations of somatic mutations that predict cancer survival and immunotherapy benefit](https://pubmed.ncbi.nlm.nih.gov/34027407/) NAR cancer, May 2021.

## Installation
CLICnet requires Python 3, and has been tested with python3.6 and python3.7. 
CLICnet can be installed using pip. From a terminal, run:

`pip install clicnet` 

This will install CLICnet and all of its dependencies.

## Installation using Conda
Conda provides an easy method to install CLICnet. First, install conda or miniconda
(https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html).

Then run the following commands to install CLICnet:
```
conda create --name clicnet python=3 pip
conda activate clicnet
pip install clicnet
```

<em>Note</em>: If you rely on conda, any time you want to use CLICnet's libraries you have to first run:
```
conda activate clicnet
```

## Usage
The primary function in the Python library is log_rank_rbm. This function runs an RBM on a provided cancer type,  
gene set and dataset. Either a pre-trained RBM can be provided, or an RBM is trained based on the provided parameters. 

CLICnet also provides several datasets, including the TCGA, MSK, Riaz et al and Liu et al datasets. The datasets are 
encoded as follows:

1. TCGA_DATA: Data from The Cancer Genome Atlas Program.
1. MSK_DATA: Data from Memorial Sloan-Kettering Cancer Center.
1. MSK_PD1_DATA: Subset of MSK data, for patients who underwent anti-PD1 treatment.
1. LIU_DATA: Data from Liu et al, of patients who underwent anti-PD1 treatment (doi:10.1038/s41591-019-0654-5).
1. RIAZ_DATA: = Data from Riaz et al, of patients who underwent anti-PD1 treatment (doi:10.1016/j.cell.2017.09.028).

<em>Note</em>: The anti-PD1 data are only available for some cancers. The list of cancers
that the anti-PD1 data are available for are saved under the clicnet.ANTI_PD1_CANCERS variable.

For example, to train an RBM on TCGA data, subset to breast cancer and BRCA1, BRCA2, and then test the resulting RBM
on the MSK data:
```
train_p_value, train_coef, train_rbm_model, cox_df, gene_set = clicnet.log_rank_rbm(
    "Breast",
    ["BRCA1", "BRCA2"],
    clicnet.TCGA_DATA,
)
test_p_value, test_coef, rbm_model, cox_df, gene_set = clicnet.log_rank_rbm(
    "Breast",
    ["BRCA1", "BRCA2"],
    clicnet.MSK_DATA,
    trained_rbm=train_rbm_model
)
```

CLICnet also allows training on the selected gene sets as published in the CLICnet manuscript, using the 
log_rank_rbm_preset function. To use this function, select the cancer and gene set index (between 1 and 5) and run:
```
# Train on clicnet selected genes
train_p_value, train_coef, train_rbm_model, cox_df, gene_set = clicnet.log_rank_rbm_preset(
    "Bladder",
    clicnet.TCGA_DATA,
    3
)
```
Testing on the MSKCC data using the selected gene set is similar:

```
# Test on clicnet selected genes
test_p_value, test_coef, rbm_model, cox_df, gene_set = clicnet.log_rank_rbm_preset(
    "Bladder",
    clicnet.MSK_DATA,
    3,
    trained_rbm=train_rbm_model
)
```

To test on melanoma anti-PD1 dataset from Liu et al and Riaz et al, run:
```
# Train on melanoma with TCGA
train_p_value, train_coef, train_rbm_model, cox_df, gene_set = clicnet.log_rank_rbm_preset(
    'Melanoma',
    clicnet.TCGA_DATA,
    3
)

# Test on Liu data
test_p_value_pd1_1, test_coef_pd1, rbm_model, cox_df, gene_set = clicnet.log_rank_rbm_preset(
    'Melanoma',
    clicnet.LIU_DATA,
    3,
    trained_rbm=train_rbm_model
)

# Test on Riaz data
test_p_value_pd1_2, test_coef_pd1, rbm_model, cox_df, gene_set = clicnet.log_rank_rbm_preset(
    'Melanoma',
    clicnet.RIAZ_DATA,
    3,
    trained_rbm=train_rbm_model
)
```

To plot the CLICnet clustering with a user-defined gene set, run (specify the training and test data):
```
# Train for Glioma with TCGA, test with MSKCC anti-PD1 treated
clicnet.log_rank_rbm_plots(
    'Glioma', clicnet.TCGA_DATA, clicnet.MSK_PD1_DATA, 
    ['ARID2', 'BCOR', 'FAT1', 'IDH1', 'MTOR', 'NCOR1', 'PIK3CD','TET2']
)
```

or for training on the one of the CLICnet published gene sets, run:
```
clicnet.log_rank_rbm_plots_preset('Glioma', clicnet.TCGA_DATA, clicnet.MSK_PD1_DATA, 2)
```

## Contact
If you run into any issues or have any questions, feel free to open an issue on Github or email us 
at either ayal.gussow@gmail.com or noamaus@gmail.com.
