# TOPMed Featured Workspace (Feb 10th backup)
###### tags: `aligner` `archive`
In this workspace, you will be running the TOPMed alignment workflow as you learn how to use the BioData Catalyst platform powered by Terra, Dockstore, and Gen3. This is beginner-oriented tutorial walks you through how each of these platforms interact with one another. We will start using a test dataset and scale up to using different data models. 

## Data Models Covered in this Tutorial
One aim of this tutorial is to help you learn multiple data models and how to interact with them so that you can easily import data from multiple sources into the TOP alignment workflow. This can help you bring your own data to the BioData Catalyst platform and compare it with the TOPMed data that is currently hosted in this system.

First, we will show you how to find data in Terra, bring it to your workspace, and use the Terra data model for the 1000 Genomes Project. 

Next, you will learn how to import TOPMed data from Gen3, which uses a more complex graph-based data model. However, in order to use TOPMed data from Gen3, you must already have access to this data that is access controlled through dbGAP.  If you don't have dbGaP access, worry not -- you'll still be able to run this workflow on open-access data we have provided.

# Step 1: Setting Up
First of all, you will need to clone this workspace to your account. You can find the button for that in the far right.

![cloning the workspace](https://raw.githubusercontent.com/aofarrel/tutorials/master/resized_clone_workspace.png)

You will need to connect a billing account to your cloned workspace. That billing account will be charged when you run the steps in this workspace. For more information on how to avoid runaway costs, see [this article here](https://support.terra.bio/hc/en-us/articles/360029748111) for an overview, or [this one](https://support.terra.bio/hc/en-us/articles/360029772212) for specific examples.

If you are planning to import controlled access data from Gen3, you should set up an Authorization Domain to protect your work. Learn more in this [article](https://support.terra.bio/hc/en-us/articles/360039415171). 

As you go through this tutorial, you will probably want to keep this page open on a separate tab so you can easily read these directions while navigating on Gen3, Dockstore, and the workflow section of Terra.

# Step 2: Using a JSON From Dockstore with Test Data
Background info (optional):
* [What is TOPMed?](https://www.nhlbiwgs.org/)
* [What is Dockstore?](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/analyze-data/dockstore) {NOTE: Currently blank, but I've drafted a Markdown file for this page.}

Now that you have copied over this workspace and connected it to a billing account, you'll be able to run the workflows contained within. For now, we will be testing our aligner on open-access data generously provided by TOPMed. Much of TOPMed data is restricted access, but this particular portion is open access. Because we will later on be going through controlled access TOPMed data, we will be calling this open access snippet "the JSON test data" for simplicity.

You might have used aligners on the command line before. Usually, this involves putting a long string of variables into the command line, often in a set order. Here on Terra we do things a little bit differently -- you have a GUI to allow you to see which files you're putting in more clearly. You also have the option of uploading a JSON file to fill this out for you. Let's try that now. 

In the top tabs of Terra, click on WORKFLOWS and you will see the alignment workflow. Select the aligner workflow (1--Aligner) and you will see a github URL after the label "source." Click on that link and it will take you to Dockstore, which is where the workflow was pulled from.

![link to take you to dockstore, which actually will say github but takes you to dockstore](https://github.com/aofarrel/tutorials/blob/master/resized_link_to_dockstore.png?raw=true)

Now, on Dockstore, click Files, then Test Parameter Files. You will see a JSON file with several google storage links -- this is what we are calling the JSOn test data. Download this file using the icon with an arrow pointing down. 

{NOTE: Right now, the test files on Dockstore have a URL structure that Terra cannot parse. Currently in talks with Walt (who maintains that repo) as to where a JSON with the correct URLs will be hosted. If it is on Dockstore, then I'll take a screenshot of how to download the JSON from there, but it might just end up already existing in this workspace.}

You can close Dockstore if you want; you won't need it for the rest of this tutorial.

Now, back to Terra. Click on "upload JSON" and select the JSON you just downloaded from Dockstore. Your fields will magically be filled in on Terra, saving you from having to type them out yourself. Now, click "save," then run your workflow! Because the JSON test data is downsampled, this test should complete in five minutes or less.

![arrow pointing to where the upload json button is](https://github.com/aofarrel/tutorials/blob/master/resized_upload_JSON.png?raw=true!)

Once it completes, a folder will be created in your workspace's DATA section that contains the output of the aligner. This is downsampled data that won't output anything useful, so we will be going over the details of finding outputs on Terra in the next section, where we're actually using full-sized CRAM files.

# Step 3: Aligning With 1000 Genomes Data
Background info (optional):
* [What is 1000 Genomes?](https://www.internationalgenome.org/about)

So we've run an alignment on test data. That's all well and good, but for far we have only used downsampled data, which isn't all that useful for writing a paper.

### Importing The Data

In this section, we will be using the 1000 Genomes as an example of "Bring Your Own Data" to the BioData Catalyst project. This Terra data model will likely familiar to you; it's a single data table that holds the Sample ID, paths to CRAMs, paths to single-sample VCFs, and metadata all together.

On the top left next to the Terra logo, you will see three bars. Click on that and you will see library. Select data from the dropdown menu and you will be taken to a collection of open-access data just waiting to be analyzed. Let's select the "1000 Genomes High Coverage presented by NHGRI AnVIL" for our purposes.

![indicator of where to get to Terra's library page](https://raw.githubusercontent.com/aofarrel/tutorials/master/resized_get_to_data'.png)

You'll be taken to a workspace for the 1000 Genomes project. Click on the DATA tab at the top and you will be taken to a page that shows more than 2000 samples. We don't need all 2504 samples for our tests, so let's click on "sample (2504)" and just select the first 5 rows. You could just import to your Terra workspace directly, but let's take a few extra steps for a moment. Download your data as a TSV to your local machine. You are NOT copying multiple gigabytes worth of data to your computer, just a TSV file that contains links to said data. *Note: If you want to bring costs down, you can use less than five rows, or even just one row. We'll continue the tutorial with five to make sure it's clear how to input arguments when dealing with multiple subjects.*

![downloading five samples of data](https://github.com/aofarrel/tutorials/blob/master/resized_sample_rows.png?raw=true)

Return to this workspace's data section and upload the TSV file. Now you have the links necessary to use this 1000 Genomes data. In addition, you now have a TSV file that include links to the data you imported, which you could upload to other workspaces if you wanted to use the same data in lots of different workspaces without selecting the same rows from 2504 subjects every time. (Most of the time you'll probably just want to import to the workspace directly, though. We just wanted to show you how TSV uploads work.)

This data is now in the Google Bucket associated with this workspace -- well, to be specific, this table is in your Google Bucket. The actual CRAM files themselves remain in the same buckets, you just now have a table that contains links to them in your workspace's Google Bucket. Each workspace has its own associated Google Bucket.

When dealing with 1000 Genomes data from Terra, you will notice this data gets uploaded as a datatable called "Samples." As we will see later on, not all datasets follow this convention.

### Running the Aligner on Your Data

Go to this workspace's workflows tab. You will see two buttons. Select the bubble labeled "Process multiple workflows from:", and in the drop down menu, select samples. This means you are selecting the 5 samples you choose from the 1000 Genomes data. What if you wanted to select less CRAMs than are in your TSV? Click "Select Data" which is located to the right of the dropdown menu. This will open a new menu where you can select precisely which rows you want to run your workflow on. In this case, each row represents a subject.
![how to select individual rows using the checkboxes on the far left side of each row](https://github.com/aofarrel/tutorials/blob/master/resized_selecting_only_some_CRAMs.png?raw=true)

Now that have selected your CRAMs, make sure to look over the arguments that you are running this program on. You'll need to set `input_cram_file` to `this.cram` in order for it to actually use the data in the table you selected -- specifically, the CRAM column. This syntax can be used to select other column names too, but all we need is the CRAM files for this workflow.
![pointing out where to type "this.cram" in the workflow page before running it](https://github.com/aofarrel/tutorials/blob/master/resized_this_dot_cram.png?raw=true)

### To Preempt or Not to Preempt

When computing on Google Cloud, you have the option of using preemptible virtual machines. These virtual machines work essentially the same as what you expect from Google Cloud, but they are significantly cheaper (sometimes less than half the price!). There is a catch, however -- they may shut down in the middle of a task and only exist for 24 hours at most. You can find more information [from Google](https://cloud.google.com/preemptible-vms/), but let's talk about the specifics of this workspace. 

This wasn't really a concern earlier on when running on tiny test CRAM files, as those files generally complete in less than ten minutes. But when running on full-size CRAM files such as those in the 1000 Genomes project, there is a chance that any given step (pre-alignment, alignment, or post-alignment) might get shut down before it completes if it's run on a preemptible VM. This is especially true of the post-align step, which tends to take the longest, often more than 24 hours when running on full-sized CRAM files.

We've broken down some possibilities here so you can balance risk, run time, and cost:


| Setting        | Max # of Tries On Preemptible VM           | Max # of Non-Preemptible Tries  |  Total Max Tries |
| ------------- |:-------------:|:-------------:|  -----:|
| `PostAlign_preemptible_tries`​ = 0 and `PostAlign_max_retries`​ = 1 |  0  | 1 | 1 |
| `PostAlign_preemptible_tries`​ = 3 and `PostAlign_max_retries`​ = 3 |  3  | 3 | 6 |
| You don't set `PostAlign_preemptible_tries`​ nor `PostAlign_max_retries`​, ie, default values are used |  0  | 3 | 3 |

Whenever `preemptible_tries` is a positive integer, the task will exhaust all preemptible tries before trying on the more expensive non-preemptible VM. The same logic applies for `PreAlign_preemptible_tries` and `PreAlign_max_retries`, as well as `Align_preemptible_tries` and `Align_max_retries`, except that unlike Post-Align the default behavior is to attempt these steps on preemptible VMs three times rather than not bothering with preemptibles at all, as these steps usually complete in under 24 hours. Terra handles all this on its own; you don't have to manually restart any of these tasks as long as the workflow itself is still running.

Once you have decided if you want to change the default behavior for preemptible VMs, press "save" and run your workflow.

### Tracking the Aligner's Progress
With your workflow now running, click on the "JOB HISTORY" tab at the top of this workspace. You will see a table detailing every submission you have made in this workspace. If you've been following this tutorial you'll see two, one for the test data, and one running on the 1000 Genomes data.

Let's click on the top row, which shows the most recently submitted workflow. You will see something like this -- metadata on the top, and below that, a one-row table. If you click on "view" in the leftmost tab of the table (marked with a square below), you will more information about the workflow, including how long it's running certain tasks. In this case, that means pre-alignment, alignment, and post-alignment. This can come in handy of a workflow fails, as this screen will tell you how long each task has run for, how many attempts it had, and (if applicable) any logs created in the process. You can also click "workflow ID" in the leftmost column (circled) to be taken to Google Cloud's own website to explore the bucket, although you can do the same on Terra itself.

Take note of the submission ID and workflow ID, or at least the first few digits of each. They'll come in handy in a moment.

![screenshot of job history page with view, workflow ID, and submission ID circled](https://github.com/aofarrel/tutorials/blob/master/resized_smaller_workflow_history.png?raw=true)

### Examining Output
As the aligner does its thing, it will begin writing files to the disk -- or, rather, to the Google Cloud bucket your workspace is hosted on. You can find this in the "Files" section of your workspace's DATA tab, below anything you have imported.

Remember that workflow ID we took note of earlier? That workflow ID is also the name of the folder that your workflow is writing to. Keep in mind *every time* you run a workflow, a new folder will be created, even if you are just running the same workflow on the same data with the same parameters. In your workspace's file system, your output data for the aligner will be stored in `Files/{submission ID}/TopMedAligner/{workflow ID}/call-PostAlign/`. So, for instance, in the screenshot above my submission ID started with "b17b9937-ff17-46e8-b20b-798dc3d4ebf2" and my workflow ID started with "2368abf3-0e2a-4cd7-b10a-89d4d1f9002d." So my output files are located in `Files / b17b9937-ff17-46e8-b20b-798dc3d4ebf2 / TopMedAligner / 2368abf3-0e2a-4cd7-b10a-89d4d1f9002d / call-PostAlign /`. Of course, these files will only be created once the aligner finishes -- if your workflow is running but hasn't finished yet, the `call-PostAlign` folder might not exist yet.


# Step 5: Using the Gen3 data model
Background info (optional, but you do need at least a basic familiarity with how Gen3 stores data):
* [How Gen3 stores data](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/explore_data/gen3-discovering-data)
* [Gen3's data dictionary](https://gen3.datastage.io/DD)
* [How Gen3 data interacts with Terra](https://support.terra.bio/hc/en-us/articles/360038087312)

Head on over to [Gen3](https://gen3.datastage.io/) and log in by clicking "Profile" in the top right hand corner. You will need to log in using your NIH eRA Commons ID. When you click on "Exploration" you will see all subjects and studies you have access to. Use filters on the right hand side of the screen to select what you are interested in. For instance, you could limit your search to subjects 

![censored view of Gen3 when you are logged in, with "export to Terra" button highlighted](https://github.com/aofarrel/tutorials/blob/master/gen3_overview_with_text_resized.png?raw=true)

After selecting a group of subjects, click the red "Export To Terra" button to do precisely that. Bear in mind that exporting may take a few minutes and you shouldn't navigate away from the page while this is happening. Once it completes, you will be taken to a Terra page where you can choose which workspace to put your data into.

### DRS: How We Keep Data Safe

Now that you're dealing with controlled-access data, you will notice that links to that data are formatted differently. If you check the JSON from step 2, you'll see that those URLs were in the gs:// form, which is used for interacting with Google Buckets. This time, you are now dealing with drs:// instead. Technically speaking these are URIs  (Uniform Resource Identifiers; a URL is a type of URI). GA4GH uses DRS (Data Repository Service) URIs for controlled access data. For more information, please see [Terra's documentation on GA4GH's Data Repository Service](https://support.terra.bio/hc/en-us/articles/360039330211).
# Step 6: What's next?
We've now gone over how to set up the TOPMed alignment workflow to work with small amounts of test data from TOPMed ("the JSON test data"),  Terra's  1000 Genomes data model, and the Gen3 graph-model . We now leave you with some final notes if you want to align your data from other sources using this workflow.
* [How to run WDLs from Dockstore](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/community_tools/dockstore-example) -- useful if you want to preform further analysis on your newly aligned data
* How to access the data in your workspace bucket from a Jupyter notebook -- a link to this would be nice

### If Your Data Was/Will Be Aligned To Something That Isn't HG38

Something that may be easy to miss in the aligner's documentation is the following note:
> The CRAM to be realigned may have been aligned with a different reference genome than what will be used in the alignment step. The pre-align step must use the reference genome that the CRAM was originally aligned with to convert the CRAM to a SAM.

There's two important things to take away from this:
1. If what you already aligned your data to (remember, CRAM/SAM/BAM files are already aligned!) does not match the reference genome you are aligning to with this workflow, you must include what you aligned them to as the argument for `PreAlign_reference_genome` and its associated index as the argument for `PreAlign_reference_genome_index`. For instance, if your CRAM files were generated using HG19 as your reference and you are using this workflow to align them HG38, you must set HG38 as your argument for `ref_fasta` and HG19 as `PreAlign_reference_genome`.
2. TOPMed data is aligned to HG38. If you are running this workflow to compare your own data to TOPMed data, you must align to HG38.


------

# Authors
Here you can cite yourself, Walt, and Jonathon instead of in the text above. You should also say this is under the BDCat project.

### Workspace change log 

| Date | Change | Author | 
| -------  | -------- | -------- |
| Feb 4, 2020 | initial draft | Ash |
| Feb 6, 2020 | updated dashboard text | Beth |
| Feb 10, 2020 | gen3, job tracking, output, major revisions | Ash |