This is a collection of workflows for the TOPMed Alignment pipeline.

The original pipelines were assembled and written by Hyun Min Kang (hmkang@umich.edu) and Adrian Tan (atks@umich.edu) at the [Abecasis Lab at the University of Michigan](https://genome.sph.umich.edu/wiki/Abecasis_Lab). See the source [alignment pipeline](https://github.com/statgen/docker-alignment) repository for more information.


## UM Aligner
WDL/CWL verison of the [TOPMed/University of Michigan Alignment](https://github.com/statgen/docker-alignment) pipeline.

#### Input:
* CRAM file
* Reference information (see JSON for Hg38 sample that you can use)

If you aligned your files to a different reference prior to running this workflow, you will need to include that previously used reference genome as `PreAlign_reference_genome`.

#### Output:
* Aligned output (CRAM)
* Index file (CRAI)


## CCDG Aligner Functional Equivalent
This pipeline implements [CCDG Pipeline standards](https://github.com/CCDG/Pipeline-Standardization/blob/master/PipelineStandard.md) specifically for high-throughput sequencing data.

#### Requirements/expectations 
- Human whole-genome pair-end sequencing data in unmapped BAM (uBAM) format
- One or more read groups, one per uBAM file, all belonging to a single sample (SM)
- Input uBAM files must additionally comply with the following requirements:
	- filenames all have the same suffix (we use ".unmapped.bam")
	- files must pass validation by ValidateSamFile
	- reads are provided in query-sorted order
	- all reads must have an RG tag
- Reference genome must be Hg38 with ALT contigs

#### Outputs 
- Aligned output (CRAM)
- Index file (CRAI)