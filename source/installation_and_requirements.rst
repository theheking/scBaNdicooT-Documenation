.. _installation_and_requirements:

Installation and Requirements
==============================

This page describes how to install scBaNdicooT and its dependencies.

Requirements
------------

scBaNdicooT is distributed as a `Nextflow <https://www.nextflow.io/>`_ pipeline. The following software must be installed before running scBaNdicooT:

.. list-table::
   :widths: 25 20 55
   :header-rows: 1

   * - Tool
     - Minimum Version
     - Purpose
   * - `Nextflow <https://www.nextflow.io/>`_
     - 23.04
     - Pipeline orchestration
   * - `Docker <https://www.docker.com/>`_ or `Singularity <https://sylabs.io/>`_
     - Any recent
     - Container runtime (recommended)
   * - `conda <https://docs.conda.io/>`_
     - 4.x
     - Alternative to containers
   * - Python
     - 3.9+
     - Required by scBaNdicooT scripts
   * - Java
     - 11+
     - Required by Nextflow

All bioinformatics tools (FastQC, Cutadapt, STAR, SAMtools) are automatically provisioned via Docker or Conda when using the appropriate Nextflow profile.

Installing Nextflow
-------------------

If you do not already have Nextflow installed, follow the official instructions or use the one-liner below:

.. code-block:: bash

   # Install Nextflow to the current directory
   curl -s https://get.nextflow.io | bash

   # Move to a directory on your PATH, e.g.:
   mv nextflow ~/bin/

   # Verify installation
   nextflow -version

.. note::
   If you are new to Nextflow, please refer to the `Nextflow Documentation <https://www.nextflow.io/docs/latest/getstarted.html>`_ for basic usage instructions. It is recommended to test your setup with a simple pipeline before running scBaNdicooT.

Installing scBaNdicooT
----------------------

The pipeline is run directly from GitHub — no separate installation is required when using Nextflow:

.. code-block:: bash

   # Pull the latest version of the pipeline
   nextflow pull theheking/scBaNdicooT

To use a specific release version:

.. code-block:: bash

   nextflow pull theheking/scBaNdicooT -revision v1.0

Installing the Python Package (Optional)
-----------------------------------------

The scBaNdicooT Python package provides the model fitting and matrix generation scripts. It is bundled in the pipeline containers but can also be installed standalone for interactive analysis:

.. code-block:: bash

   pip install scbandicoot

Or from source:

.. code-block:: bash

   git clone https://github.com/theheking/scBaNdicooT.git
   cd scBaNdicooT
   pip install -e .

Software Dependencies
---------------------

The following tools are used internally by the pipeline and are automatically installed via the container or conda profile:

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Tool
     - Version
     - Role
   * - FastQC
     - 0.12+
     - Read quality control
   * - Cutadapt
     - 4.x
     - Adapter and quality trimming
   * - STAR
     - 2.7+
     - RNA-seq alignment
   * - SAMtools
     - 1.17+
     - BAM sorting and indexing
   * - MultiQC
     - 1.14+
     - Aggregated QC reporting

Reference Genome
----------------

scBaNdicooT requires a pre-built STAR genome index. To build one for mouse (GRCm39):

.. code-block:: bash

   STAR --runMode genomeGenerate \
        --genomeDir /path/to/star_index \
        --genomeFastaFiles GRCm39.fa \
        --sjdbGTFfile gencode.vM33.annotation.gtf \
        --runThreadN 8

For human (GRCh38):

.. code-block:: bash

   STAR --runMode genomeGenerate \
        --genomeDir /path/to/star_index \
        --genomeFastaFiles GRCh38.fa \
        --sjdbGTFfile gencode.v44.annotation.gtf \
        --runThreadN 8
  