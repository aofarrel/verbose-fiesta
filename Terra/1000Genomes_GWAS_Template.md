# GWAS Tutorial in NHLBI's BioData Catalyst

This tutorial workspace offers example tools for conducting mixed-models GWAS from start to finish using the [NHLBI BioData Catalyst](https://biodatacatalyst.nhlbi.nih.gov/) ecosystem. We've created a set of documents [to get you started in the BioData Catalyst system](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/analyze-data/terra). If you're ready to conduct an analysis, proceed with this dashboard: 

![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

## Data Model
This template was set up to work with the NHLBI BioData Catalyst Gen3 data model. In this dashboard, you'll learn how to import open access data from the Gen3 platform into this Terra template and conduct an association test. If you have never used the Gen3 data model before, we suggest you start with the tutorial [Getting Started with Gen3 Data in Terra](https://terra.biodatacatalyst.nhlbi.nih.gov/#workspaces/fc-product-demo/BioDataCatalyst-Gen3-data-on-Terra-Tutorial).

For this tutorial, we are using synthetic phenotypic data coupled with downsampled 1000 Genomes data that has been ingested into NHLBI BioData Catalyst Powered by Gen3. This data model is likely new to most users and may take some time to become accustomed to. First, the data model is based on a graph structure that is more complex than a single columns x rows data table that you may be familiar with. Second, genomic data is accessible through DRS URLs that point to Google Cloud Buckets that hold the data. 

After this tutorial, you can employ the BioData Catalyst ecosystem for more analyses. Currently, BioData Catalyst's Gen3 hosts the [TOPMed](https://www.nhlbi.nih.gov/science/trans-omics-precision-medicine-topmed-program) program, which is controlled access, in addition to the public data used in this tutorial. To apply for access to TOPMed, submit an application through [dbGAP](https://www.nhlbiwgs.org/topmed-data-access-scientific-community). If you already have access to a TOPMed project and have been onboarded to the BioData Catalyst platform, you should be able to access your data through BioData Catalyst Powered By Gen3 and use your data with another GWAS resource we have created that you can access [here](https://app.terra.bio/#workspaces/biodata-catalyst/BioData%20Catalyst%20GWAS%20blood%20pressure%20trait). 

![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

## About the data
To demonstrate an analysis that could be run on typical whole genome sequence data, this workspace provides mock phenotype data generated from publicly available 1000 Genomes phase 3 genotypes. Phenotypes have been simulated based on individual genotypes and known associated loci for multiple complex traits. The GCTA software was used with lists of causal variants and an estimate of narrow sense heritability for each phenotype.

Traits and sources for causal variants  
a. BMI: Giant-UKBB meta-analysis  
b. Fasting glucose: MAGIC  
c. Fasting insulin: MAGIC  
d. Waist-to-hip ratio: GIANT-UKBB meta-analysis  
e. Height: GIANT-UKBB meta-analysis  
f. HDL: MVP  
g. LDL: MVP  
h. Total cholesterol: MVP  
i. Triglycerides: MVP  

**Generating the synthetic data**
The scripts used to create the phenotype data, as well as intermediate data files and a readme file, are in a public Google bucket (gs://terra-featured-workspaces/GWAS/data_processing/). To browse the Google bucket, click here.

![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

## Tutorial outline
***Part 1: Navigate the BioData Catalyst environment***
Learn how to search and export data from Gen3 and worfklows from Dockstore into a Terra workspace. 

***Part 2: Reformat Gen3 phenotypic data for use in downstream analysis***
Review the data you imported in Terra and use the interactive Jupyter notebook **1-Prepare-Gen3-data-for-exploration** to consolidate several clinical data tables into a single data table that can be used in the next notebook. This notebook calls functions in the companion notebook **terra_data_table_util**. 

***Part 3: Data exploration and preparation***
The **2-GWAS-preliminary-analysis** notebook will lead you through a series of steps to explore the phenotypic and genotypic data and prepare files for import into association workflows. This is the most time and resource consuming analysis in the GWAS.

***Part 4: Perform mixed-model association tests using workflows***
Next, perform mixed models genetic association tests (run as a series of batch workflows using GCP Compute engine). The workflows are also publicly available in [Dockstore](https://dockstore.org/) in this [collection](https://dockstore.org/organizations/bdcatalyst/collections/GWAS).       

![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

# Part 1: Navigate the NHLBI BioData Catalyst ecosystem

## 1a. Link your Terra account to Gen3 using external services
Before you're able to access genomic data from Gen3 in the Terra data table, you need to link your Terra account to external services. Link your profile [by following these instructions](https://support.terra.bio/hc/en-us/articles/360038086332?flash_digest=2f492682b688b21da27c701af68656ac095d5803).

## 1b. Create an Authorization Domain to protect your controlled-access data
If you bring controlled access data into Terra, it should be registered under an Authorization Domain that limits its access to only researchers with the appropriate approvals. Learn how to set up an Authorization Domain [here](https://support.terra.bio/hc/en-us/articles/360039415171).

## 1c. Export the training dataset from Gen3 to Terra
1. Start by learning about Gen3's graph-structured data model for NHLBI's BioData Catalyst using this [orientation document](https://support.terra.bio/hc/en-us/articles/360038087312).
2. Once you better understand the graph, log into [Gen3](https://gen3.biodatacatalyst.nhlbi.nih.gov/) through the NIH portal using your eRA Commons username and password. 
3. Navigate to the [Gen3 Explorer](https://gen3.biodatacatalyst.nhlbi.nih.gov/explorer) view to see what datasets you currently have and do not have access to. On the left hand side, you can use the faceted search tool to narrow your results to specific projects. 
4. First, under "Files" and "Access", select "Data with Access" to filter through projects that you currently have access to.
5. Next, under "Filters", select the "Subject" tab. 
6. In the "Project Id" filter, there is a small search bar, you can type in "tutorial".  Select the project "tutorial-synthetic_data_set_1". 
7.  Once selected, click the button "Export all to Terra", and then proceed to section 1d below. 

## 1d. Select a Terra workspace for your data
Once the export window in Gen3 transitions to Terra, you are given a few options for where to place your data:

1) "Start with a template"
This feature allows you to import data directly into a template workspace that has everything set up for you to do an analysis but does not contain any data. Once you select a workspace, you will need to enter:
- Workspace name: Enter a name a name that is meaningful for your records.
- Billing Project: Select the billing projects available to you. If you are a new user, you can use the [$300 of free credits offered](https://support.terra.bio/hc/en-us/articles/360027940952-Free-credits-FAQs).
- Authorization Domain: Assign the authorization domain that you generated above to protect your data. This is important for working with controlled access. data. 

2) "Start with an existing workspace" 
If you have already created a workspace, you can import your data directly to this workspace. 

3) "Start a new workspace"
This will create an empty workspace. You can individually copy notebooks and workflows from other workspaces, import workflows from Dockstore, or start fresh.

![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

# Part 2: Reformat Gen3 phenotypic data for use in downstream analysis
If you have not used the Gen3 data model before, we recommend you first start with this tutorial [Getting Started with Gen3 Data in Terra](https://terra.biodatacatalyst.nhlbi.nih.gov/#workspaces/fc-product-demo/BioDataCatalyst-Gen3-data-on-Terra-Tutorial) and then come back to this step in the GWAS.

1. Once data has been successfully uploaded to your Terra account, review the Data tab of your Terra workspace. The tutorial dataset should include the following clinical data tables that you will use in the GWAS: Subject, Demographic, Lab Result, Sample. 
2. Navigate to the Notebooks tab and open **1-Prepare-Gen3-data-for-exploration**. This notebook consolidates the clinical data tables mentioned in step 1 into a single data table that can be used in the GWAS. This notebook calls functions in the companion notebook **terra_data_table_util**. You don't have to open terra_data_table_util notebook, it is available for users to edit for other use cases.
3. Once this notebook completes, you should go back to the Data tab in your workspace and check out the new data table "consolidated_metadata". This data table has all of the phenotypic data merged into one table. Now you are ready to import your data into the first GWAS analysis notebook. 

Note: This consolidated_metadata table is closer to the data model you use in our "Featured Workspaces".  We hope these two notebooks (1-Prepare-Gen3-data-for-exploration, terra_data_table_util) help you use Gen3 data with more of our training resources in Terra.       

![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

# Part 3: Explore TOPMed data in Jupyter Notebooks   
Now that you can interact with the Gen3 structured data more easily, you will use an interactive notebook to explore your phenotypic and environmental data and performs several analyses to prepare the data for use in batch association workflows. 

1. [Learn how to customize your interactive analysis compute](https://support.terra.bio/hc/en-us/articles/360038125912) to work with the data you imported. Your computing needs will vary depending on the size of your VCF file. 
3. Open the **2-GWAS-preliminary-analysis** notebook and set your runtime configuration. We have given a suggested configuration within the notebook for a downsampled VCF (representing chromosomes 10 and 11) that we include for training purposes.
4. Call functions from the terra_data_util notebook to reformat multiple data tables into a single data table that can be loaded as a dataframe in the notebook.
5. Subset the dataframe to include only your traits of interest and remove any individuals that lack data for these traits.
6. Visualize phenotype and environmental variable distributions in a series of plots.
8. Filter your VCF to only common variants to increase statistical power. Genetic analyses in this notebook utilize the [Hail software](https://hail.is/). Hail is a framework for distributed computing with a focus on genetics. Particularly relevant for whole genome sequence ([WGS](https://en.wikipedia.org/wiki/Whole_genome_sequencing)) analysis, Hail allows for efficient, nearly boundless computing (in terms of variant and sample size).    
9. Perform a principal component analysis ([PCA](https://en.wikipedia.org/wiki/Principal_component_analysis)) to assess population stratification. Genetic stratification can strongly affect association tests and should be accounted for.
10. Generate a genetic relatedness matrix ([GRM](https://hail.is/docs/0.2/methods/genetics.html?highlight=pc_rel#hail.methods.genetic_relatedness_matrix)) to account for closely related individuals in your association testing workflows.
11. Generate a new "sample_set" data table that holds the derived files we created in the steps above using the [FireCloud Service Selector (FISS) package](https://github.com/broadinstitute/fiss).  The files in this data table will be used in the workflows we run in Part 4.     


Note: VCF files you import from Gen3 are in the Reference_File node and are accessible via DRS URLs. For TOPMed Freeze 5b datasets, these are also tar compressed. We have this GWAS tutorial that has notebooks and examples for interacting with these VCFs [here](https://terra.biodatacatalyst.nhlbi.nih.gov/#workspaces/biodata-catalyst/BioData%20Catalyst%20GWAS%20blood%20pressure%20trait).

### Time and cost estimate 
Time to execute all the commands is ~28 minutes which currently costs ~$0.50 to complete (with the recommended cluster configuration available inside the notebook).       

![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

# Part 4: Perform mixed-model association tests using workflows
In Part 3, we explored the data we imported from Gen3 and performed a few important steps for preparing our data for association testing. We generated a new "sample_set" data table that holds the files we created in the interactive notebook. These files will be used in our batch workflows that will perform the association tests. Below, we describe the four workflows in this workspace and their cost estimates for running on the sample set we create in this tutorial.  

The workflows used in this template were imported from [Dockstore](www.dockstore.org) and their parameters were configured to work with Terra's data model.  If you're interested in searching other docker-based workflows, [learn more about how they can easily be launched in Terra](https://support.terra.bio/hc/en-us/articles/360038137292).

## Notes on how attributes are set in workflows
We have set the input and output attributes for each workflow in this template. Before running the first workflow, you can look through the inputs and outputs of each workflow and see that outputs from the first workflow feed into the second workflow, and so on. 

In the 2-GWAS-preliminary-analysis notebook, we created a Sample Set data table that holds a row called "tutorial-analysis" which contains the input files for the following workflows. You can check this data table out in the Data tab of this workspace. When you open a workflow, make sure that "Sample Set" is set and the "tutorial-analysis" (or whatever you named your run) is selected before running a workflow. 


#### [1-vcfToGds](https://dockstore.org/workflows/github.com/manning-lab/vcfToGds)

This workflow converts genotype files from Variant Call Format ([VCF](https://en.wikipedia.org/wiki/Variant_Call_Format)) to Genomic Data Structure ([GDS](https://www.biostat.washington.edu/sites/default/files/modules/GDS_intro.pdf)), the input format required by the R package GENESIS.       

##### Time and cost estimates    

| Sample Set Name | Sample Size | Time | Cost |
| -------  | --------  | -------- | -------- |
| tutorial-analysis | 2,504 samples | 6m | $0.07

Inputs:
* VCF  genotype file (or chunks of VCF files)

Outputs:
* GDS genotype file

#### [2-genesis_GWAS](https://dockstore.org/workflows/github.com/AnalysisCommons/genesis_wdl/genesis_GWAS)

This workflow creates a null model from phenotype data with the GENESIS biostatistical package. This null model can then be used for association testing. This workflow also runs single variant and aggregate test for genetic data. Implements Single-variant, Burden, SKAT, SKAT-O and SMMAT tests for Continuous or Dichotomous outcomes. All tests account for familiar relatedness through kinship matrixes. Underlying functions adapted from: Conomos MP and Thornton T (2016). GENESIS: GENetic EStimation and Inference in Structured samples (GENESIS): Statistical methods for analyzing genetic data from samples with population structure and/or relatedness. R package version 2.3.4.        

##### Time and cost estimates    

| Sample Set Name | Sample Size | Time | Cost |
| -------  | -------- | -------- | ---------- |
|  tutorial-analysis | 2,504 samples | 6m | $0.07

Inputs:
* GDS genotype file
* Genetic Relatedness Matrix
* Trait outcome name
* Trait outcome type
* CSV file of covariate traits
* Sample ID list

Outputs:
* A null model as an RData file
* Compressed csv file(s) containing raw results
* CSV file containing all associations
* CSV file containing top associations
* PNG file of Quantile-Quantile and Manhattan plots


![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

# Optional: Bring your own data
Both the notebook and workflows can be adapted to other genetic datasets. The steps for adapting these tools to another dataset are outlined below:

***Update the data tables***  
Learn more about uploading data to Terra [here](https://support.terra.bio/hc/en-us/articles/360025758392-Managing-data-with-the-data-table-).  

***Update the notebook***
Accommodating other datasets may require modifying many parts of this notebook. Inherently, the notebook is an interactive analysis where decisions are made as you go. It is not recommended that the notebook be applied to another dataset without careful thought. 

***Run an additional workflow***
You can search [Dockstore](www.dockstore.org) for available workflows and export them to Terra following [this method](https://docs.dockstore.org/en/develop/launch-with/terra-launch-with.html). 


![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)  

### Authors, contact information, and funding
This template was created for the [NHLBI's BioData Catalyst](https://biodatacatalyst.nhlbi.nih.gov/) project in collaboration with the [Computational Genomics Platform](https://cgpgenomics.ucsc.edu/) at [UCSC Genomics Institute](https://ucscgenomics.soe.ucsc.edu/) and the [Data Sciences Platform](https://www.broadinstitute.org/data-sciences-platform) at [The Broad Institute](https://www.broadinstitute.org/). The association analysis tools were contributed by the [Manning Lab](https://manning-lab.github.io/).

Contributing authors include:
* [Beth Sheets](mailto:esheets@ucsc.edu) (UC Santa Cruz Genomics Institute)
* Michael Baumann (Broad Institute, Data Sciences Platform)
* Brian Hannafious (UC Santa Cruz Genomics Institute)
* [Tim Majarian](mailto:tmajaria@broadinsitute.org) (Manning Lab)
* Alisa Manning (Manning Lab)


![white space](https://storage.cloud.google.com/terra-featured-workspaces/QuickStart/white-space.jpg)     

----

### Workspace change log 
| Date | Change | Author | 
| -------  | -------- | -------- |
| March 13, 2020 | Created notebook | Beth |
| April 7, 2020 | Reviewed dashboard and tested notebooks and workflows | Beth |
| June 26, 2020 | terra_data_table_util & codefolding updates | Beth |
| December 7, 2020 | Updated notebooks | Ash |