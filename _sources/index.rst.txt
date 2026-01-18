.. ANS documentation master file, created by
   sphinx-quickstart on Thu Oct  5 10:24:37 2023.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

ANS: Adjusted Neighborhood Scoring
==================================
Gene signature scoring is integral to single-cell RNA sequencing (scRNA-seq) data analysis, particularly for unsupervised cellular state annotation based on maximum signature score values. However, this application requires robust and comparable score distributions across diverse signatures and experimental conditions. Our systematic evaluation of established scoring methodologies—Seurat, SCANPY, UCell, and JASMINE—across nine healthy and cancer scRNA-seq datasets demonstrates their insufficiency in fulfilling this requirement. To address this limitation, we present Adjusted Neighborhood Scoring (ANS), a deterministic algorithm with enhanced control gene selection that significantly improves score stability and cross-signature comparability, achieving cell state annotation accuracy comparable to supervised methods. We demonstrate the practical utility of ANS by developing and validating a gene signature to differentiate cancer-associated fibroblasts from malignant cells undergoing epithelial-to-mesenchymal transition. Overall, ANS provides a robust and reliable gene signature scoring framework, significantly improving the accuracy of score-based annotation of cell types and states in single-cell studies.

.. image:: img/website_figure.png
  :width: 700
  :alt: Performance comparison of ANS and other scRNA-seq scoring methods on cell-type annotation.
  :align: center

.. note::
   A preprint describing ANS and showing the results of signature scoring methods benchmark is  `now available <https://doi.org/10.1101/2023.09.20.558114>`_.

Navigation
----------
.. toctree::
   :maxdepth: 2
   :caption: Contents:

   tutorials
   api


* :ref:`search`


Getting started
---------------

Installation
^^^^^^^^^^^^
We aim for Python versions 3.8+. Run:

.. code-block:: bash

   pip install git+https://github.com/BoevaLab/ANS_signature_scoring.git

*Disclaimer*: The implementations of all Tirosh et al. 2016 based scoring methods are largely based on the implementation of the :func:`score_genes` method in `Scanpy <https://scanpy.readthedocs.io>`_.


Basic usage in Python
^^^^^^^^^^^^^^^^^^^^^
The package allows full compatibility with the Python scRNA-seq analysis toolbox `Scanpy <https://scanpy.readthedocs.io>`_. The scoring methods are applied on preprocessed (log-normalized) scRNA-seq.

.. code-block:: python

    import signaturescoring as ssc

    ssc.score_signature(
        adata=adata,                            # preprocessed (log-normalized) gene expression data in an AnnData object
        gene_list=gene_signature,               # gene expression signature, type list
        method='adjusted_neighborhood_scoring',
        ctrl_size=100,
        score_name='scores',                    # scores stored in adata.obs column defined by score_name
    )

    print(adata.obs['scores'].describe())


Other `method` values:

- `seurat_scoring`, `seurat_ag_scoring`, and `seurat_lvg_scoring`: Python implementation of the scoring method `AddModuleScore <https://satijalab.org/seurat/reference/addmodulescore>`_ of the package `Seurat <https://satijalab.org/seurat/>`_ first proposed by `Tirosh et al. 2016 <https://doi.org/10.1126/science.aad0501>`_ and two alternatives (this paper).
- `jasmine_scoring`: Python implementation of `JASMINE <https://github.com/NNoureen/JASMINE>`_ by `Noureen et al. 2022 <https://github.com/NNoureen/JASMINE>`_. Requires an additional argument `score_method` with the values `likelihood` or `oddsratio`.
- `ucell_scoring`: Python implementation of `UCell <https://github.com/carmonalab/UCell>`_ by `Andreatta et Carmona 2021 <https://doi.org/10.1016/j.csbj.2021.06.043>`_.


Basic usage in R
^^^^^^^^^^^^^^^^
The repository contains an R implementation of the novel scoring method in the folder `src_R/adjusted_neighborhood_scoring.R`. The file can be downloaded, and the method can be loaded for usage.

*Disclaimer*: The code is largely based on the implementation of the `AddModuleScore` method of the `Seurat <https://satijalab.org/seurat/>`_ package.

Note: ANS for R should be used on Seurat objects. Source the file in your script and use it identically to `AddModuleScore <https://satijalab.org/seurat/reference/addmodulescore>`_.

Example:

.. code-block:: R

   source('MT/ANS_signature_scoring/src_R/adjusted_neighborhood_scoring.R')

   # Initialize the Seurat object with the log-normalized data.
   # e.g. Peripheral Blood Mononuclear Cells (PBMC) freely available from 10X Genomics
   pbmc <- "..."

   # List of signatures
   markers <- list(markers = gene_list)

   # score data
   pbmc <- AdjustedNeighborhoodScoring(pbmc, features = markers)


