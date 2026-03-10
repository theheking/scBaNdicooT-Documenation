.. _testing_data:

Testing Data
============

To help users validate their installation and learn the expected input/output format, scBaNdicooT provides a small test dataset derived from a publicly available nascent single-cell RNA-seq experiment.

Test Dataset Description
------------------------

The test dataset is a downsampled subset of the MMTV-PyMT ALTEN case study data (see :ref:`mmtv_pymt_alten_model`), designed to run quickly on a laptop or a small compute node.

.. list-table::
   :widths: 30 70
   :header-rows: 1

   * - Property
     - Value
   * - **Species**
     - *Mus musculus* (mouse)
   * - **Genome build**
     - GRCm39 (mm39)
   * - **Labelling**
     - 4-thiouridine (4sU), 2-hour pulse
   * - **Platform**
     - BD Rhapsody
   * - **Samples**
     - 2 (control and Doxorubicin-treated)
   * - **Reads per sample**
     - ~500,000 (downsampled)
   * - **Expected runtime**
     - ~20 minutes on 4 CPUs

Downloading the Test Data
-------------------------

The test FASTQ files and associated reference files can be downloaded from the scBaNdicooT GitHub releases page:

.. code-block:: bash

   # Download test data archive
   wget https://github.com/theheking/scBaNdicooT/releases/download/v1.0/test_data.tar.gz

   # Extract
   tar -xzf test_data.tar.gz
   cd test_data/

The extracted directory will contain:

.. code-block:: text

   test_data/
   ├── samplesheet.csv          # Pre-filled samplesheet
   ├── fastq/
   │   ├── control_R1.fastq.gz
   │   ├── control_R2.fastq.gz
   │   ├── doxorubicin_R1.fastq.gz
   │   └── doxorubicin_R2.fastq.gz
   ├── genome/
   │   └── star_index/          # Pre-built STAR index (GRCm39, chr19 only)
   └── annotation/
       └── gencode.vM33.chr19.gtf

Running the Test Pipeline
-------------------------

Once test data is downloaded, run the pipeline with the ``test`` profile:

.. code-block:: bash

   nextflow run theheking/scBaNdicooT \
       --samplesheet test_data/samplesheet.csv \
       --genome test_data/genome/star_index \
       --gtf test_data/annotation/gencode.vM33.chr19.gtf \
       --outdir test_results/ \
       -profile docker

Alternatively, using the ``-profile test`` shortcut (requires internet access to pull the test data automatically):

.. code-block:: bash

   nextflow run theheking/scBaNdicooT -profile test,docker

Expected Output
---------------

After a successful test run, the ``test_results/`` directory should contain:

* Two aligned BAM files (one per sample).
* Nascent, total, and NTR count matrices in ``test_results/scbandicoot/``.
* FastQC HTML reports in ``test_results/fastqc/``.
* A MultiQC summary in ``test_results/multiqc/``.
* Per-sample model diagnostic reports in ``test_results/scbandicoot/reports/``.

The log should end with a ``Succeeded`` status for all pipeline processes.

Troubleshooting
---------------

If the test run fails, check the following:

* **Docker/Singularity**: Ensure your container runtime is installed and running.
* **Nextflow version**: scBaNdicooT requires Nextflow ≥ 23.04.
* **Memory**: The test profile is configured for 8 GB RAM. Reduce ``--max_memory`` if needed.
* **Logs**: Inspect ``test_results/pipeline_info/execution_trace.txt`` for detailed per-process resource usage and error messages.
