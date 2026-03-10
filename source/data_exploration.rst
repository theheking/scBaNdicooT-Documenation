.. _data_exploration:

Data Exploration
================

This section describes the downstream analysis and visualisation of the scBaNdicooT output matrices, using the MMTV-PyMT ALTEN dataset as a case study.

Loading scBaNdicooT Output
--------------------------

scBaNdicooT produces three count matrices per sample. These can be loaded directly into standard single-cell analysis frameworks:

.. code-block:: python

   import scanpy as sc
   import numpy as np

   # Load total, nascent, and NTR matrices
   adata_total    = sc.read_10x_mtx("results/scbandicoot/total/sample1/")
   adata_nascent  = sc.read_10x_mtx("results/scbandicoot/nascent/sample1/")
   adata_ntr      = sc.read_10x_mtx("results/scbandicoot/ntr/sample1/")

   # Store layers
   adata_total.layers["nascent"] = adata_nascent.X
   adata_total.layers["ntr"]     = adata_ntr.X

Normalisation and Dimensionality Reduction
------------------------------------------

Standard scRNA-seq preprocessing is applied to the **total** count matrix:

.. code-block:: python

   # Normalise and log-transform
   sc.pp.normalize_total(adata_total, target_sum=1e4)
   sc.pp.log1p(adata_total)

   # Identify highly variable genes
   sc.pp.highly_variable_genes(adata_total, n_top_genes=3000)

   # PCA
   sc.pp.pca(adata_total)

   # Neighbourhood graph and UMAP
   sc.pp.neighbors(adata_total, n_pcs=30)
   sc.tl.umap(adata_total)

Cell Type Annotation
--------------------

Cell types are annotated using known marker genes for the cell populations present in MMTV-PyMT tumoroids:

.. list-table::
   :widths: 30 70
   :header-rows: 1

   * - Cell Type
     - Key Marker Genes
   * - Epithelial / tumour cells
     - *Epcam*, *Krt8*, *Krt18*, *Krt14*
   * - Cancer-associated fibroblasts
     - *Col1a1*, *Vim*, *Fsp1* (*S100a4*)
   * - Macrophages
     - *Csf1r*, *Cd68*, *Adgre1* (F4/80)
   * - T cells
     - *Cd3e*, *Cd8a*, *Cd4*
   * - Endothelial cells
     - *Pecam1*, *Cdh5*, *Vegfr2* (*Kdr*)

.. code-block:: python

   # Leiden clustering
   sc.tl.leiden(adata_total, resolution=0.5)

   # Visualise marker genes
   sc.pl.umap(adata_total, color=["leiden", "Epcam", "Col1a1", "Cd68"])

Nascent RNA Visualisation
--------------------------

The nascent count layer can be used to visualise active transcription on the UMAP embedding:

.. code-block:: python

   import matplotlib.pyplot as plt

   # Compute per-cell total nascent UMIs
   adata_total.obs["nascent_umi_count"] = np.array(
       adata_total.layers["nascent"].sum(axis=1)
   ).flatten()

   # Plot
   sc.pl.umap(adata_total, color="nascent_umi_count", cmap="Reds",
              title="Total nascent UMIs per cell")

Differential Nascent Expression
---------------------------------

To identify genes with significantly higher nascent transcription in the Doxorubicin-treated condition, use the NTR or nascent count layer:

.. code-block:: python

   # Use nascent counts for differential expression
   adata_total.X = adata_total.layers["nascent"]

   sc.tl.rank_genes_groups(
       adata_total,
       groupby="condition",
       groups=["doxorubicin"],
       reference="control",
       method="wilcoxon"
   )

   sc.pl.rank_genes_groups_dotplot(
       adata_total,
       groupby="condition",
       n_genes=10,
       title="Top nascent DEGs: Doxorubicin vs Control"
   )

NTR-Based Trajectory Analysis
-------------------------------

The nascent-to-total ratio (NTR) can be used as a proxy for RNA velocity, enabling trajectory inference without requiring spliced/unspliced count estimation:

.. code-block:: python

   import scvelo as scv

   # Assign NTR as velocity proxy
   adata_total.layers["velocity"] = (
       adata_total.layers["ntr"] - adata_total.layers["ntr"].mean(axis=0)
   )

   # Compute velocity graph
   scv.tl.velocity_graph(adata_total, vkey="velocity")
   scv.pl.velocity_embedding_stream(
       adata_total, basis="umap", vkey="velocity",
       title="NTR-based transcriptional trajectory"
   )

Integration Across Samples
---------------------------

Multiple samples (e.g., control and Doxorubicin-treated) can be integrated using Harmony or scVI:

.. code-block:: python

   import harmonypy as hm

   # Concatenate samples
   adata_combined = adata_control.concatenate(
       adata_dox, batch_categories=["control", "doxorubicin"]
   )

   # Re-run preprocessing
   sc.pp.normalize_total(adata_combined, target_sum=1e4)
   sc.pp.log1p(adata_combined)
   sc.pp.highly_variable_genes(adata_combined, batch_key="batch", n_top_genes=3000)
   sc.pp.pca(adata_combined)

   # Harmony integration
   sc.external.pp.harmony_integrate(adata_combined, key="batch")
   sc.pp.neighbors(adata_combined, use_rep="X_pca_harmony")
   sc.tl.umap(adata_combined)

   sc.pl.umap(adata_combined, color=["batch", "leiden", "Epcam"],
              title="Integrated MMTV-PyMT ALTEN dataset")

Subclonal Analysis
------------------

scBaNdicooT output can be combined with copy number variation (CNV) inference (e.g., using `InferCNV <https://github.com/broadinstitute/inferCNV>`_ or `CopyKAT <https://github.com/navinlabcode/copykat>`_) to distinguish transcriptional reprogramming from clonal selection:

.. code-block:: python

   # After running CopyKAT or InferCNV, assign CNV scores to adata
   adata_total.obs["cnv_score"] = cnv_scores  # from CopyKAT output

   # Correlate nascent DEG response with CNV score
   sc.pl.scatter(
       adata_total[adata_total.obs["cell_type"] == "epithelial"],
       x="cnv_score",
       y="nascent_umi_count",
       color="condition"
   )
