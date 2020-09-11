# TOPMed Aligner (Parred Down)
###### tags: `aligner`

In this workspace, you will be running the TOPMed alignment workflow as you learn how to use the BioData Catalyst platform powered by Terra and Dockstore. This is beginner-oriented tutorial walks you through how each of these platforms interact with one another. We will start using a test dataset and scale up to using different data models.

Please note that if you are looking for information about Gen3 and its data model, please see this template workspace instead. INSERT LINK HERE !!! !!! !!! !!! 

## Data Models Covered in this Tutorial
One aim of this tutorial is to help you learn how to interact with data models already on Terra. This can help you bring your own data to the BioData Catalyst platform and compare it with the TOPMed data that is currently hosted in this system.

First, we will run through the aligner with test data, showing you how to import from Dockstore. Then, we will show you how to find data in Terra, bring it to your workspace, and use the Terra data model for the 1000 Genomes Project. At the end, we will give you instructions on running this aligner on your own data.

# Setting Up
First of all, you will need to clone this workspace to your account. See [Terra's documentation on that](https://support.terra.bio/hc/en-us/articles/360026130851-How-to-clone-a-workspace) if needed. You will need to connect a billing account to your cloned workspace. That billing account will be charged when you run the steps in this workspace. For more information on how to avoid runaway costs, see [this article here](https://support.terra.bio/hc/en-us/articles/360029748111) for an overview, or [this one](https://support.terra.bio/hc/en-us/articles/360029772212) for specific examples. 

# Using a JSON From Dockstore with Test Data
Background info (optional):
* [What is TOPMed?](https://www.nhlbiwgs.org/)
* [What is Dockstore?](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/analyze-data/dockstore)
* [How to use JSONs on Terra to run workflows faster](https://support.terra.bio/hc/en-us/articles/360027735471-Getting-workflows-up-and-running-faster-with-a-JSON-file)

Now that you have copied over this workspace and connected it to a billing account, you'll be able to run the workflows contained within. For now, we will be testing our aligner on open-access data generously provided by TOPMed. Much of TOPMed data is controlled access, but this particular portion is unrestricted.

You might have used aligners on the command line before. Usually, this involves putting a long string of variables into the command line, often in a set order. Here on Terra we do things a little bit differently -- you have a GUI to allow you to see which files you're putting in more clearly. You also have the option of uploading a JSON file to fill this out for you. Let's try that now.  In the top tabs of Terra, click on WORKFLOWS and you will see the alignment workflow. Select the aligner workflow.

Now, on [the original Dockstore page this aligner came from](https://dockstore.org/workflows/github.com/DataBiosphere/topmed-workflows/UM_aligner_wdl:develop?tab=files), click Files, then Test Parameter Files. You will see a JSON file with several google storage links -- this is what we are calling the JSON test data. Download this file using the icon with an arrow pointing down. You can close Dockstore if you want; you won't need it for the rest of this tutorial.

{NOTE: This JSON is only on /develop right now.}

Now, back to Terra. Click on "upload JSON" and select the JSON you just downloaded from Dockstore. Your fields will magically be filled in on Terra, saving you from having to type them out yourself. (See Terra's documentation linked at the top of this section if you're having trouble.) Now, click "save," then run your workflow! Because the JSON test data is downsampled, this test should complete in five minutes or less.

Once it completes, a folder will be created in your workspace's DATA section that contains the output of the aligner. This is downsampled data that won't output anything useful, so we will be going over the details of finding outputs on Terra in the next section, where we're actually using full-sized CRAM files.

# Step 3: Aligning With 1000 Genomes Data (Terra's Data Model)
Background info (optional):
* [What is 1000 Genomes?](https://www.internationalgenome.org/about)
* [Understanding data in the cloud](https://support.terra.bio/hc/en-us/articles/360034335332-Understanding-Data-in-the-Cloud)

So we've run an alignment on test data. That's all well and good, but for far we have only used downsampled data, which isn't all that useful for writing a paper. Next, let's move onto something a bit more substantial: Full-sized CRAMs!

### Importing The Data

In this section, we will be using the 1000 Genomes as an example of "Bring Your Own Data" to the BioData Catalyst project. This Terra data model will likely familiar to you; we will be importing a single data table that holds the Sample ID, paths to CRAMs, paths to single-sample VCFs, and metadata all together.

On the top left next to the Terra logo, you will see three bars. Click on that and you will see library. Select data from the dropdown menu and you will be taken to a collection of open-access data just waiting to be analyzed. Let's select the "1000 Genomes High Coverage presented by NHGRI AnVIL" for our purposes.

![indicator of where to get to Terra's library page](https://github.com/aofarrel/tutorials/blob/master/resized_bdc_terra_data.png?raw=true)

You'll be taken to a workspace for the 1000 Genomes project. Click on the DATA tab at the top and you will be taken to a page that shows more than 2000 samples. We don't need all 2504 samples for our tests, so let's click on "sample (2504)" and just select the first 5 rows. You could just import to your Terra workspace directly, or download a TSV. Let's just import directly. Once this completes, we can return to this workspace.

Now you have the links necessary to use this 1000 Genomes data. This data is now in the Google Bucket associated with this workspace -- well, to be specific, this table is in your Google Bucket. The actual CRAM files themselves remain in the same buckets, you just now have a table that contains links to them in your workspace's Google Bucket. Each workspace has its own associated Google Bucket. When dealing with 1000 Genomes data from Terra, you will notice this data gets uploaded as a datatable called "Samples." As we will see later on, not all datasets follow this convention.

### Running the Aligner on Your Data

Go to this workspace's workflows tab. You will see two buttons. Select the bubble labeled "Process multiple workflows from:", and in the drop down menu, select samples. This means you are selecting the 5 samples you choose from the 1000 Genomes data. What if you wanted to select less CRAMs than are in your data table? Click "Select Data" which is located to the right of the dropdown menu. This will open a new menu where you can select precisely which rows you want to run your workflow on. In this case, each row represents a subject.

![how to select individual rows using the checkboxes on the far left side of each row](https://github.com/aofarrel/tutorials/blob/master/re-re-resized_selecting_only_some_CRAMs.png?raw=true)

Now that have selected your CRAMs, make sure to look over the arguments that you are running this program on. You'll need to set `input_cram_file` to `this.cram` in order for it to actually use the data in the table you selected -- specifically, the CRAM column. This syntax can be used to select other column names too, but all we need is the CRAM files for this workflow. **Please note that your inputs, such as "this.cram" are case sensitive.**

![pointing out where to type "this.cram" in the workflow page before running it](https://github.com/aofarrel/tutorials/blob/master/re-re-resized_this_dot_cram.png?raw=true)

### To Preempt or Not to Preempt

When computing on Google Cloud, you have the option of using preemptible virtual machines. These virtual machines work essentially the same as what you expect from Google Cloud, but they are significantly cheaper (sometimes less than half the price!). There is a catch, however -- they may shut down in the middle of a task and only exist for 24 hours at most. You can find more information [from Google](https://cloud.google.com/preemptible-vms/), but let's talk about the specifics of this workspace. 

This wasn't really a concern earlier on when running on tiny test CRAM files, as those files generally complete in less than ten minutes. But when running on full-size CRAM files such as those in the 1000 Genomes project, there is a chance that any given step (pre-alignment, alignment, or post-alignment) might get shut down before it completes if it's run on a preemptible VM. This is especially true of the post-align step, which tends to take the longest, often more than 24 hours when running on full-sized CRAM files.

We've broken down some possibilities here so you can balance risk, run time, and cost:


| Setting        | Max # of Tries On Preemptible VM           | Max # of Non-Preemptible Tries  |  Total Max Tries |
| ------------- |:-------------:|:-------------:|  -----:|
| `PostAlign_preemptible_tries`​ = 0 and `PostAlign_max_retries`​ = 1 |  0  | 1 | 1 |
| `PostAlign_preemptible_tries`​ = 3 and `PostAlign_max_retries`​ = 3 |  3  | 3 | 6 |
| You don't set `PostAlign_preemptible_tries`​ nor `PostAlign_max_retries`​, ie, default values are used |  0  | 3 | 3 |

Whenever `preemptible_tries` is a positive integer, the task will exhaust all preemptible tries before trying on the more expensive non-preemptible VM. The same logic applies for `PreAlign_preemptible_tries` and `PreAlign_max_retries`, as well as `Align_preemptible_tries` and `Align_max_retries`, except that unlike Post-Align the default behavior is to attempt these steps on preemptible VMs three times rather than not bothering with preemptibles at all, as these steps usually (but not always) complete in under 24 hours. Terra handles all this on its own; you don't have to manually restart any of these tasks as long as there are still tries left.

What you ultimately decide depends on how you wish to balance time and cost. If you want a run to finish as fast as possible, do not use preemptibles. If you putting this on before a holiday weekend, you should probably use preemptibles for the prealign and align steps to save money. With that being said, there is a chance that in the worst case, if your prealign and align steps consistently fail on preemptibles, your overall cost might end up higher. This is unlikely to happen with Terra's 1000 Genomes data but might happen with larger CRAMs. 

Once you have decided if you want to change the default behavior for preemptible VMs, press "save" and run your workflow.

### Tracking the Aligner's Progress
With your workflow now running, click on the "JOB HISTORY" tab at the top of this workspace. You will see a table detailing every submission you have made in this workspace. If you've been following this tutorial you'll see two, one for the test data, and one running on the 1000 Genomes data.

Let's click on the top row, which shows the most recently submitted workflow. You will see something like this -- metadata on the top, and below that, a one-row table. If you click on "view" in the leftmost tab of the table (marked with a square below), you will more information about the workflow, including how long it's running certain tasks. In this case, that means pre-alignment, alignment, and post-alignment. This can come in handy of a workflow fails, as this screen will tell you how long each task has run for, how many attempts it had, and (if applicable) any logs created in the process. You can also click "workflow ID" in the leftmost column (circled) to be taken to Google Cloud's own website to explore the bucket, although you can do the same on Terra itself.

Take note of the submission ID and workflow ID, or at least the first few digits of each. They'll come in handy in a moment.

![screenshot of job history page with view, workflow ID, and submission ID circled](https://github.com/aofarrel/tutorials/blob/master/cropped_resized_smaller_workflow_history.png?raw=true)

### Examining Output
As the aligner does its thing, it will begin writing files to the disk -- or, rather, to the Google Cloud bucket your workspace is hosted on. You can find this in the "Files" section of your workspace's DATA tab, below anything you have imported.

Remember that workflow ID we took note of earlier? That workflow ID is also the name of the folder that your workflow is writing to. Keep in mind *every time* you run a workflow, a new folder will be created, even if you are just running the same workflow on the same data with the same parameters. In your workspace's file system, your output data for the aligner will be stored in `Files/{submission ID}/TopMedAligner/{workflow ID}/call-PostAlign/`. So, for instance, in the screenshot above my submission ID started with "b17b9937-ff17-46e8-b20b-798dc3d4ebf2" and my workflow ID started with "2368abf3-0e2a-4cd7-b10a-89d4d1f9002d." So my output files are located in `Files / b17b9937-ff17-46e8-b20b-798dc3d4ebf2 / TopMedAligner / 2368abf3-0e2a-4cd7-b10a-89d4d1f9002d / call-PostAlign /`. Of course, these files will only be created once the aligner finishes -- if your workflow is running but hasn't finished yet, the `call-PostAlign` folder might not exist yet.

# Bring Your Own Data (BYOD)
We'll now move onto a typical Bring Your Own Data (BYOD) situation. While Gen3 hosts TOPMed data and the open-access 1000 Genomes project, you may have your own data somewhere out there that you're looking to bring into Terra. Exactly how you do that will depending on where that data is stored We can't account for every possible scenario here, but we tried to handle the most common ones.

There is a section with common pitfalls after these sections, please see if it you have any errors.

### My data is...

### ...in a public Google Cloud bucket
Earlier on in this tutorial, we used downsampled TOPMed data to test the alignment workflow. If you completed that part of the tutorial, then you already know one option you have when bringing your own data.

Take another look at that JSON, and you will see that the input files are in the form of gs:// URIs. If your data is hosted in a public Google Cloud bucket, simply fill out the inputs of your workspace with your own data's URIs. **Please note that you MUST use the gs:// format, not the https://storage.googleapis.com/ format.**

### ...behind DRS URIs
In order to use data behind DRS URIs, [you will need to have your Terra account set up for controlled access data](https://support.terra.bio/hc/en-us/articles/360037648172-Accessing-TCGA-Controlled-Access-workspaces-in-Terra). Once this is set up, you can use DRS URIs as if they were gs:// URIs, that is, putting them in as direct inputs to your workflows. **Note that some workflows may require modification to work with DRS URIs. (For instance, our good friend Walt at UCSC had to adjust the variant caller's WDL for this workspace!)**

### ...on Gen3
Please see this template workspace INSERT LINK HERE!!! !!!!!! ! !!!! !!!! in order to directly import Gen3 data into a workspace.

### ...on my local machine
You can upload data directly from your local machine to Terra using Terr's UI. [Please see Option 1 in Terra's documentation here](https://support.terra.bio/hc/en-us/articles/360024056512-Uploading-to-a-workspace-Google-bucket). Because you are uploading directly to your workspace bucket in this method, your data can easily be accessed by workflows.

### ...on a public FTP server
As with the Google Bucket solution, you can use Terra's terminal to move files that are on a public FTP server. Terra has a terminal system for users who prefer the command line for certain tasks. We'll be using this to our advantage.

For demonstration purposes, let's use the public FTP server of 1000 Genomes data. This will download a small readme file. Open Terra's terminal (leftmost icon in the "notebook runtime" box at the top right of your webpage) and enter the following line:

`wget ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/technical/retired_reference/ancestral_alignments/README
`

If you want to download all the contents of the `ancestral_alignments` directory instead of just one file, just use an asterick:

`wget ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/technical/retired_reference/ancestral_alignments/*
`

Next, [follow the steps here to move your files into your workspace bucket](https://hackmd.io/@AshedPotatoes/rkEb7PTHL).

### ...is in an AWS bucket, such as s3
This works basically the same as if it were in a Google Bucket, as gsutil is interoperable with Amazon's storage system. You can use gsutil in either [Terra's terminal](https://hackmd.io/@AshedPotatoes/rkEb7PTHL) or your local terminal. [Please see Google Cloud's documentation for setting up your AWS credentials.](https://cloud.google.com/storage/docs/interoperability)

### ...somewhere else
Generally speaking, your easiest option is to use gsutil in [Terra's terminal](https://hackmd.io/@AshedPotatoes/rkEb7PTHL) (or your local machine's terminal, if you prefer). In any case, the command for gsutil in most circumstances is:
`gsutil cp [file you want to move] [gs URI of your workspace Google Bucket]`


## Using Data That's In Your Workspace Data Section As An Input For A Workflow
*(Not necessary reading if you already know your data's URI)*

In order to run workflows on your data, you need to know the Google Storage address that your newly uploaded data is now residing. Click on your newly uploaded file in Terra's data view. A window will pop up that will include a bar saying "Terminal download command," which will give you the address where the file now lives. 

![screenshot showing the popup when you click on a file in Terra's data view](https://raw.githubusercontent.com/aofarrel/tutorials/master/get%20name%20of%20file.png)

You can copy paste that address directly into a workflow input. So, for example, the line "terminal download command" for this CRAM is `gsutil cp gs://fc-d5bc9024-8e8d-47bd-94ac-de6ca5f58ee8/NWD119836.chr1_10000000-11000000.recab.cram .` and to enter this as an argument for the alignment workflow, you would use "gs://fc-d5bc9024-8e8d-47bd-94ac-de6ca5f58ee8/NWD119836.chr1_10000000-11000000.recab.cram" (note the lack of period at the end, and yes, the quotation marks are required).


## Resolving Common Pitfalls with BYOD
#### "Requester pays" error
In summary: You can ignore this. When you go to download a file from a Google Cloud Bucket, you may be charged to download that file. This is the case when the file is in a "requester pays" bucket. However, when first attempting to download the file, the downloader doesn't know what kind of bucket it is in. Therefore, any system attempting to download from a Google Cloud Bucket will first see if it can be done without paying. If the data is in requester pays bucket, you will see an error in your log files. As standard behavior is to immediately try downloading again, this time paying for the privilege of doing so, you can ignore this error provided your billing account is set up correctly.

#### Can't find the + button to upload data directly into a workspace
The + button is located on the lower right of your browser window. Make sure you're scrolled all the way down.

#### Can't find downloaded data in the workspace's data section
Make sure to follow the directions under the header "How to Move Files Between the Terminal and Your Workspace Bucket" in [the documentation for Terra's terminal](https://hackmd.io/@AshedPotatoes/rkEb7PTHL).

#### Using Terra's terminal
[Please see documentation here](https://hackmd.io/@AshedPotatoes/rkEb7PTHL) for an introduction to using it and the most common troubleshooting topics.

#### If Your Data Was/Will Be Aligned To Something That Isn't HG38

Something that may be easy to miss in the aligner's documentation is the following note:
>The CRAM to be realigned may have been aligned with a different reference genome than what will be used in the alignment step. The pre-align step must use the reference genome that the CRAM was originally aligned with to convert the CRAM to a SAM.

There's two important things to take away from this:
1. If what you already aligned your data to (remember, CRAM/SAM/BAM files are already aligned!) does not match the reference genome you are aligning to with this workflow, you must include what you aligned them to as the argument for `PreAlign_reference_genome` and its associated index as the argument for `PreAlign_reference_genome_index`. For instance, if your CRAM files were generated using HG19 as your reference and you are using this workflow to align them HG38, you must set HG38 as your argument for `ref_fasta` and HG19 as `PreAlign_reference_genome`.
2. TOPMed data is aligned to HG38. If you are running this workflow to compare your own data to TOPMed data, you must align to HG38.


# What's next?
We've now gone over how to set up the TOPMed alignment workflow to work with small amounts of test data from TOPMed ("the JSON test data"),  Terra's  1000 Genomes data model, the Gen3 graph-model, and finally, your own data. We now leave you with some final notes:
* [How to run WDLs from Dockstore](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/community_tools/dockstore-example) -- useful if you want to preform further analysis on your newly aligned data

If you are looking to run the aligner on Gen3 data, please see the template workspace here for instructions.


------

# Authors
Workspace author: Ash O'Farrell  
WDL script and bug fixing: Walt Shands  
Dockerized TopMED Aligner: Jonathon LeFaive  
Edits and bug-squashing: Michael Baumann, Beth Sheets  


### Workspace change log 

| Date | Change | Author | 
| -------  | -------- | -------- |
| Feb 4, 2020 | initial draft | Ash |
| Feb 6, 2020 | updated dashboard text | Beth |
| Feb 10, 2020 | gen3, job tracking, output, major revisions | Ash |
| Feb 18, 2020 | prepared for peer review | Ash |
| Mar 19, 2020 | implemented BYOD | Ash |