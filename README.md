## scFEA: A graph neural network model to estimate cell-wise metabolic using single cell RNA-seq data

## Change Log

### v1.0
- Release scFEA initial version with full paper and installtion manual
- Release human complete metabolic flux modules

### v1.1
- Update full modules with metabolites names.
- Update `data_dir` and `input_dir` two directories. `data_dir` is for model files and `input_dir` is for single cell input files.
- Fix bugs in single cell imputation step by using [Magic](https://www.krishnaswamylab.org/projects/magic).

### v1.1.2
- Release mouse complete metabolic flux modules
- Release [tutorial](https://github.com/changwn/scFEA/blob/master/scFEA_tutorial1.ipynb) using jupyter. The tutorial contains full installation manual, installation testing, two examples of scFEA for human and mouse model respectively.  It also includes R script for loading predicted flux result and a simple visialuzation demo.
- Add parameter `output_flux_file` and `output_balance_file` which allows user define custom output file names for predicted flux and balance files.
- Fix bug in *cName_c70_m168.csv* which contains `NA` value

### To be released soon 
If you have an interested topic, please feel free to open an [issue](https://github.com/changwn/scFEA/issues) or I can also merge your completed function into main branch
- [ ] release scFEA sampling and fitting function for large dataset. For dataset which contains more than 10,000 single cells, the general pipeline is too time comsuming to get the result without a GPU acceleration. Instead, user should use sampling function to extract subset of cells for each cell type to train a model. Then use fitting function to predict metabolic flux on all cells.
- [ ] tutorial of how to use sampling function and fitting function on a large dataset
- [ ] evaluation of predicted flusome (we will provide a few of examples to show how to evaluate the goodness of fitting based on the convergence curves and cross validations)
- [ ] cell clustering function based on predicted metabolic flux and graph structure, cell clustering visualization
- [ ] perturbation analysis to identify high impact metabolic genes
- [x] [tutorial](https://github.com/changwn/scFEA/blob/master/scFEA_tutorial2.ipynb) for embedding scFEA pipeline into a Seurat object and the Seurat pipeline for biological backgroud user who are familiar with Seurat 
- [ ] spatial transcriptomic data analysis (spatial dependent metabolic and biochemical changes)
- [ ] more visualization functions
- [ ] integrate downstream analysis and visualization related function into scFEA R package
- [ ] scFEA web server which enable online analysis and figure generation (will be released in December 2021)



## Abstract

The metabolic heterogeneity, and metabolic interplay between cells and their microenvironment have been known as significant contributors to disease treatment resistance. Our understanding of the intra-tissue metabolic heterogeneity and cooperation phenomena among cell populations is unfortunately quite limited, without a mature single cell metabolomics technology. To mitigate this knowledge gap, we developed a novel computational method, namely **scFEA** (**s**ingle **c**ell **F**lux **E**stimation **A**nalysis), to infer single cell fluxome from single cell RNA-sequencing (scRNA-seq) data.  scFEA is empowered by a comprehensively reorganized human metabolic map as focused metabolic modules, a novel probabilistic model to leverage the flux balance constraints on scRNA-seq data, and a novel graph neural network based optimization solver. The intricate information cascade from transcriptome to metabolome was captured using multi-layer neural networks to fully capitulate the non-linear dependency between enzymatic gene expressions and reaction rates. We experimentally validated scFEA by generating an scRNA-seq dataset with matched metabolomics data on cells of perturbed oxygen and genetic conditions. Application of scFEA on this dataset demonstrated the consistency between predicted flux and metabolic imbalance with the observed variation of metabolites in the matched metabolomics data. We also applied scFEA on publicly available single cell melanoma and head and neck cancer datasets, and discovered different metabolic landscapes between cancer and stromal cells. The cell-wise fluxome predicted by scFEA empowers a series of downstream analysis including identification of metabolic modules or cell groups that share common metabolic variations, sensitivity evaluation of enzymes with regards to their impact on the whole metabolic flux, and inference of cell-tissue and cell-cell metabolic communications.

## The computational framework of scFEA

<p align="center">
  <img width="60%" src="https://github.com/changwn/scFEA/blob/master/doc/Figure%201.png">
</p>

## The manuscript and supplementary methods

Our paper and supplementary methods is available at [here](https://genome.cshlp.org/content/early/2021/07/22/gr.271205.120.abstract)!

## Supplementary figures and tables

[donwload supplementary files](https://github.com/changwn/scFEA/tree/master/supplementary%20data)

Supplementary Tables:



- Table S1. Information of reorganized human metabolic map.

- Table S2. Differentially expressed genes (DEG) and Pathway Enrichment (PE) results of the Pa03c cell line data.

- Table S3. ssGSEA results, metabolomics data and clusters of metabolic modules derived in the Pa03c cell line data.

- Table S4. Predicted cell type specific fluxome and metabolic imbalance in the melanoma and head and neck cancer data.


Supplementary Figures:

- Figure S1. qRT-PCR results. Mock and SCR are controls and siRef-1 are knock down of APEX1.

- Figure S2. Correlation between metabolomic difference of the eight metabolites and differences of the averaged ssGSEA score of the modules using the eight metabolites as a substrate, in the APEX1-KD cells vs control. The x-axis is the difference of averaged ssGSEA score in the APEX1-KD cells vs control and the y-axis is the fold change of observed metabolomic profile.

- Figure S3. The impact of each gene to the metabolic module 1-14 (glycolysis and TCA cycle modules) in the Pa03c cell line data. The x-axis represents genes and y-axis represents impacts. The larger absolute value on the y-axis indicates a stronger impact of the gene to the metabolic module.

- Figure S4. tSNE plot of the cell clusters generated based on metabolic flux of the pancreatic cancer cell line data.

- Figure S5. Boxplots of the predicted fluxes of Valine -> Succinyl-CoA, Isoleucine -> Succinyl-CoA, Isoleucine -> Acetyl-CoA, Glutathione -> Glycine + Cysteine, Glutathione -> Glutamate, Glutamate -> Glutamine and predicted changes in the abundance of Glutathione and Glutamate in the PV-ADSC of high stemness (HS) and more differentiation (MD).

- Figure S6. Convergency of the flux balance loss and non-negative loss during the training of scFEA on the pancreatic cancer cell line data. The hyper parameters of the two loss were set differently to form four experiments. The flux balance loss, non-negative loss and total loss were blue, red and black-dash colored.


## Requirements and Installation

scFEA is implemented by Python3. If you don't have python, please download [Anaconda](https://www.anaconda.com/download/#linux) with python 3 version.

- torch >= 0.4.1
- numpy >= 1.15.4
- pandas >= 0.23.4
- matplotlib >=3.0.2
- magic >= 2.0.4

Download scFEA:
```
git clone https://github.com/changwn/scFEA
```

Install requirements:
```
cd scFEA
conda install --file requirements
conda install pytorch torchvision -c pytorch
pip install --user magic-impute
```

## Usage

You can see the input arguments for scFEA by help option:
```
python src/scFEA.py --help
usage: scFEA.py [-h] [--data_dir <data_directory>]
                [--input_dir <input_directory>] [--res_dir <data_directory>]
                [--test_file TEST_FILE] [--moduleGene_file MODULEGENE_FILE]
                [--stoichiometry_matrix STOICHIOMETRY_MATRIX]
                [--sc_imputation {True,False}]

scFEA: A graph neural network model to estimate cell-wise metabolic flux using
single cell RNA-seq data

optional arguments:
  -h, --help            show this help message and exit
  --data_dir <data_directory>
                        The data directory for scFEA model files.
  --input_dir <input_directory>
                        The data directory for single cell input data.
  --res_dir <data_directory>
                        The data directory for result [output]. The output of scFEA includes two matrices, predicted metabolic flux and metabolites
                        stress at single cell resolution.
  --test_file TEST_FILE
                        The test SC file [input]. The input of scFEA is a single cell profile matrix, where row is gene and column is cell. Example
                        datasets are provided in /data/ folder. The input can be raw counts or normalised counts. The logarithm would be performed
                        if value larger than 30.
  --moduleGene_file MODULEGENE_FILE
                        The table contains genes for each module. We provide human and mouse two models in scFEA. For human model, please use
                        module_gene_m168.csv which is default. All candidate moduleGene files are provided in /data/ folder.
  --stoichiometry_matrix STOICHIOMETRY_MATRIX
                        The table describes relationship between compounds and modules. Each row is an intermediate metabolite and each column is
                        metabolic module. For human model, please use cmMat_171.csv which is default. All candidate stoichiometry matrices are
                        provided in /data/ folder.
  --cName_file CNAME_FILE
                        The name of compounds. The table contains two rows. First row is compounds name and second row is corresponding id.
  --sc_imputation {True,False}
                        Whether perform imputation for SC dataset (recommend set to <True> for 10x data).


```

**Run code with default parameters:**
```
python src/scFEA.py
```

**Other example:**
```
python src/scFEA.py --input_dir data --res_dir output --test_file Melissa_full.csv
```


## Citation

If you find our work helpful in your resarch or work, please cite us.


N. Alghamdi, W. Chang, P. Dang, X. Lu, C. Wan, Z. Huang, J. Wang, M. Fishel, S. Cao, C. Zhang. scFEA: A graph neural network model to estimate cell-wise metabolic using single cell RNA-seq data, under review at *Genome Research*, 2020. 



## Questions & Problems

If you have any questions or problems, please feel free to open a new issue [here](https://github.com/changwn/scFEA/issues). We will fix the new issue ASAP.  For code questions, please contact Wennan Chang.

- [Wennan Chang](https://changwn.github.io/)
(wnchang@iu.edu)

For any other further questions or requests, please contact the Principle Investigator of BDRL lab.
- [Prof. Chi Zhang](https://zcslab.github.io/)
(czhang87@iu.edu)

PhD candidate at [Biomedical Data Research Lab (BDRL)](https://zcslab.github.io/) , Indiana University School of Medicine
