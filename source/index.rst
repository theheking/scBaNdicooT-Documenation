.. scBaNdicooT documentation master file, created by
   sphinx-quickstart on Thu Jan  8 12:40:30 2026.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

scBaNdicooT: Single-Cell Beta-Mixture Nascent Detection in Complex Tumour Tissue 
==================================================================================


.. figure:: ../img/fig_pipeline-01.png
   :class: with-border
   :alt: Visual abstract summarising the nascent single-cell RNA sequencing framework
   :align: center
   :width: 90%

Understanding how individual cells respond to therapy, in real time, is one of the biggest challenges in cancer biology. Traditional bulk and single-cell RNA sequencing provide powerful snapshots of gene expression, but they struggle to distinguish newly activated transcriptional programs from long-lived, pre-existing RNA. This limitation makes it difficult to capture the earliest molecular decisions that determine whether a cell becomes sensitive, resistant, or committed to cell death.

This project introduces a nascent single-cell RNA sequencing framework designed to overcome that challenge. By combining metabolic RNA labelling with single-cell sequencing, we directly measure newly synthesised RNA at single-cell resolution. This enables a time-resolved view of transcriptional activity — revealing how cells dynamically respond to stress, drugs, and environmental cues, rather than relying on steady-state expression alone.

In the paper, we apply this approach in a physiologically relevant 3D tumour model, preserving tumour architecture and the native tumour microenvironment. Using ex vivo tumoroids derived from a genetically engineered mouse model of breast cancer, we profile transcriptional responses to chemotherapy across thousands of individual cells, spanning epithelial, immune, endothelial, and stromal populations.

Crucially, this work extends nascent RNA sequencing to scalable single-cell platforms, including a newly developed pipeline compatible with microwell-based technologies, such as BD Rhapsody. This fills a major methodological gap between low-throughput plate-based methods and rigid droplet-based workflows, making nascent single-cell analysis more accessible for complex tissues and large datasets.

This resource is intended for researchers who want to investigate dynamic gene regulation in heterogeneous cell populations by applying nascent RNA sequencing to heterogeneous tissues. Specifically, it provides bioinformatics steps for analysing nascent single-cell RNA-seq data generated using:

- BD Rhapsody or similar microwell-based single-cell platforms

- 4sU-based metabolic labelling to track newly transcribed RNA as per scNT-seq

I. The scBaNdicooT Nextflow Pipeline
------------------------------------
The core of this project is a scalable, automated Nextflow pipeline designed for high-throughput processing of nascent single-cell data. This pipeline addresses a major methodological gap by providing a compatible workflow for microwell-based technologies, specifically BD Rhapsody.

**Key Computational Features:**
* **Scalable Barcode Processing**: Optimised to handle the complex barcode geometry of BD Rhapsody microwell platforms.
* **Probabilistic Modeling**: Uses a two-component Beta-mixture model to distinguish true 4sU/EU-induced T-to-C conversions from background sequencing errors.
* **EM Algorithm**: Employs Expectation-Maximisation (EM) to estimate parameters (π, α, β) representing the nascent fraction and background noise.
* **Automated Quantification**: Generates separate gene-by-cell matrices for nascent, total, and nascent-to-total ratios (NTR) for downstream analysis.


II. Nascent Single-Cell Framework
---------------------------------
Traditional scRNA-seq captures a snapshot of total mRNA, which is often dominated by long-lived, pre-existing transcripts. scBaNdicooT utilises metabolic RNA labelling (4sU/EU) to provide a time-resolved view of transcriptional activity.

* **Real-Time Dynamics**: Measures newly synthesised RNA to reveal how cells dynamically respond to stress and drugs in real time.
* **Kinetic Resolution**: Captures the earliest molecular decisions determining cell fate (sensitivity vs. resistance) before they are visible in the steady-state transcriptome.

III. Application in Complex 3D Tumour Models
--------------------------------------------
We demonstrate the power of scBaNdicooT by profiling transcriptional responses to chemotherapy in a physiologically relevant **3D tumour model**.

* **ALTEN System**: Uses Alginate-based tissue engineering to encapsulate **MMTV-PyMT** mammary tumour fragments, preserving the native 3D architecture and microenvironment.
* **Drug Response**: Profiles thousands of individual cells—including epithelial, immune, and stromal populations—to identify subclonal responses to Doxorubicin.
* **Subclonal Selection**: Integrates nascent RNA detection with CNV profiling to distinguish between immediate drug-induced reprogramming and the selection of resistant subclones.




References
==========

.. rubric:: Key nascent single-cell RNA sequencing methods

- Erhard, F. *et al.* (2019). **scSLAM-seq reveals core features of transcription dynamics in single cells**. *Nature*.  
- Hendriks, G.-J. *et al.* (2019). **NASC-seq monitors RNA synthesis in single cells**. *Nature Communications*.  
- Cao, J. *et al.* (2020). **Joint profiling of chromatin accessibility and nascent transcription in single cells**. *Nature Biotechnology*.  
- Qiu, X. *et al.* (2022). **scNT-seq enables dynamic profiling of transcriptional responses at single-cell resolution**. *Nature Methods*.  
- Qiu, X. *et al.* (2023). **scNT-seq2 improves sensitivity and scalability of nascent transcript detection in single cells**. *Nature Biotechnology*.

.. toctree::
   :maxdepth: 1
   :caption: Pipeline Documentation:

   installation_and_requirements
   nextflow_workflow_details
   testing_data
   beta_mixture_modeling

.. toctree::
   :maxdepth: 1
   :caption: Case Study:

   mmtv_pymt_alten_model
   quality_control
   data_exploration
