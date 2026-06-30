# Discovering Patterns in Neuronal Connectivity and Cellular Identity: Bridging SNR and MERFISH Data Through Soma Location

Statistical project using unsupervised and supervised learning techniques to investigate the relationship between neuronal connectivity, anatomical position and cellular identity in the mouse entorhinal cortex.

The project combines two complementary datasets:

- The **SNR dataset**, which contains single-neuron reconstructions and describes both where individual neurons send their axons and where their somata are located in the brain.
- The **MERFISH dataset**, which contains spatial transcriptomic data for a much larger number of cells, describing gene expression while preserving anatomical location information.

## Overview

This project analyses a subset of Single Neuron Reconstruction data containing tracing information for **237 neurons** extracted from **11 mouse brains**. The dataset includes **67 features**, including neuron identifiers (`mouseID`, `neuronNR`, `neuron_ID`), spatial coordinates (`x`, `y`, `z`) in the Allen Mouse Brain Common Coordinate Framework, and projection-area variables describing axonal length and endpoint counts across target regions.

The SNR analysis is then related to evidence from the MERFISH dataset, which contains spatial transcriptomic information for **48,615 entorhinal cortex cells** and **8,460 genes**. The shared spatial coordinates make it possible to compare connectivity-defined neuronal groups with gene-expression-based cellular identity patterns.

The aim of the project was to identify axonal projection patterns and assess whether projection-defined neuronal groups are associated with soma location and MERFISH-derived molecular labels.

## Methodology

The analysis was carried out in Python and included:

- Exploratory Data Analysis of SNR projection variables and MERFISH gene-expression data
- Preprocessing and standardisation of connectivity, spatial and transcriptomic features
- Dimensionality reduction using Principal Component Analysis (PCA)
- Connectivity-based clustering of SNR neurons using hierarchical clustering, K-means++ and spectral clustering
- Spatial classification of projection-defined SNR labels using soma coordinates
- Comparison of several supervised classifiers, including Logistic Regression, Random Forest, SVM with RBF kernel and XGBoost
- Gene-expression clustering of MERFISH cells
- Transfer of predicted SNR-like connectivity labels to MERFISH cells
- Enrichment analysis between MERFISH gene-expression clusters and predicted SNR-like labels
- Permutation testing and Benjamini-Hochberg correction for multiple testing

## Data

### SNR dataset

The SNR dataset contains spatial and connectivity information for 237 neurons in layer 5a of the entorhinal cortex. Projection variables are recorded using two main suffixes:

- `_endpoint`: number of axon terminals observed in a given projection area
- `_length`: total axonal length observed in a given projection area

For the final clustering pipeline, only the axonal length variables were retained, because they provide a more informative measure of projection strength and avoid redundancy with endpoint counts.

### MERFISH dataset

The MERFISH dataset contains spatial transcriptomic information for neurons in the entorhinal cortex. It is stored as an AnnData object, where rows correspond to cells and columns correspond to genes.

The dataset includes:

- gene-expression values
- spatial coordinates in the Allen Common Coordinate Framework
- brain region annotations
- transcriptomic labels such as `class`, `subclass` and `supertype`

In this project, gene-expression values were used to identify molecular cell groups, while spatial coordinates were used to relate MERFISH cells to the SNR-based connectivity analysis.

## Models and analysis

### SNR connectivity clustering

The SNR connectivity matrix was built using the 29 axonal length variables. After standardisation, PCA was applied and the first **18 principal components** were retained, explaining approximately **90%** of the variance.

Several clustering methods were compared:

1. **Agglomerative hierarchical clustering**
   - Average and complete linkage were considered
   - Cosine dissimilarity was used to compare relative projection patterns

2. **K-means++**
   - Used as a compact, centroid-based clustering baseline

3. **Spectral clustering**
   - Used as a more flexible graph-based method
   - Implemented with an RBF kernel

The selected SNR clustering solution was **spectral clustering with two clusters** and **gamma = 0.17**, chosen primarily on the basis of silhouette score.

### Spatial classification

The projection-defined SNR cluster labels were then used as the target variable in a supervised classification task. The predictors were the soma coordinates (`x`, `y`, `z`).

The following classifiers were compared:

- Logistic Regression
- Logistic Regression with pairwise coordinate interactions
- Random Forest
- Support Vector Machine with RBF kernel
- XGBoost

Model selection focused on out-of-sample predictive performance on a held-out test set. Macro F1-score was used as the main metric because the SNR cluster labels were imbalanced.

The final selected classifier was an **SVM with RBF kernel**, which achieved the best overall test-set performance among the models considered.

### MERFISH gene-expression clustering

For MERFISH, the standardised gene-expression matrix was reduced using PCA. The first **150 principal components** were retained, explaining approximately **90%** of the variance.

Gene-expression clustering was then performed on the PCA representation. The final selected solution was **hierarchical clustering with average linkage and cosine dissimilarity**, producing **11 MERFISH clusters**.

### SNR-MERFISH comparison

The selected SNR spatial classifier was applied to the MERFISH soma coordinates to assign each MERFISH cell a predicted SNR-like connectivity label.

These predicted labels were compared with the MERFISH gene-expression clusters using enrichment analysis. Enrichment was defined as the ratio between the frequency of a predicted SNR-like label within a MERFISH cluster and its overall frequency in the MERFISH dataset.

Permutation testing was used to assess whether the observed enrichment values were larger than expected under random label assignment. Benjamini-Hochberg correction was then applied to control the false discovery rate across multiple cluster-label comparisons.

## Results

The analysis identified two main projection-defined SNR groups with distinct connectivity patterns. These groups were partly predictable from soma location, suggesting that neuronal connectivity is spatially organised within the entorhinal cortex.

When the spatial classifier was transferred to the MERFISH dataset, the predicted SNR-like labels were not uniformly distributed across MERFISH gene-expression clusters. Some transcriptional clusters showed significant enrichment or depletion of specific predicted connectivity-related labels after permutation testing and multiple-testing correction.

Overall, the results suggest a partial relationship between neuronal connectivity, anatomical position and gene-expression-based cellular identity. The enrichment patterns indicate that some molecularly defined groups may be associated with distinct predicted connectivity profiles.

## Limitations

Several limitations should be considered:

- The SNR dataset is relatively small, with only 237 neurons, which limits the stability of both clustering and supervised classification.
- The MERFISH cells do not have directly observed axonal projections, so the assigned SNR-like labels are predicted connectivity-related labels rather than measured projection identities.
- The transfer from SNR to MERFISH relies on the assumption that spatial relationships learned from the SNR dataset can be meaningfully applied to the MERFISH dataset.
- A more robust assessment of predictive performance would require repeated train-test resampling, bootstrap validation or permutation testing of the full classification pipeline.
- The biological interpretation of the identified clusters remains exploratory and would require additional validation using independent data or experimental evidence.

## Possible improvements

Future work could improve the analysis by:

- Increasing the number of single-neuron reconstructions used for connectivity-based clustering
- Repeating the supervised classification pipeline across multiple train-test splits to assess stability
- Testing alternative spatial models for the relationship between soma location and projection-defined labels
- Incorporating additional molecular annotations from the MERFISH taxonomy
- Comparing the predicted connectivity labels with independently measured projectomic or BARseq data
- Performing a more detailed biological interpretation of the enriched MERFISH clusters

## Data availability

The raw datasets are not included in this repository because they are subject to data access restrictions and cannot be shared publicly.

Please read ()

## Report

The full dissertation report is available [here](report/S2882823_dissertation_final.pdf).
