# TOPMed Aligner (Template Workspace Version)

In this workspace, you will be running the TOPMed alignment workflow as you learn how to use the BioData Catalyst platform powered by Terra, Dockstore, and Gen3. This is beginner-oriented tutorial walks you through how each of these platforms interact with one another. We will start using a test dataset and scale up to using different data models.

## Data Models Covered in this Tutorial
One aim of this tutorial is to help you learn multiple data models and how to interact with them so that you can easily import data from multiple sources. This can help you bring your own data to the BioData Catalyst platform and compare it with the TOPMed data that is currently hosted in this system.

You will learn how to import TOPMed data from Gen3, which uses a more complex graph-based data model. However, in order to use TOPMed data from Gen3, you must already have access to this data that is access controlled through dbGAP.  If you don't have dbGaP access, worry not -- you'll still be able to run this workflow on open-access data we have provided.

# Using the Gen3 data model
Background info (optional, but you do need at least a basic familiarity with how Gen3 stores data):
* [How Terra stores data in tables](https://support.terra.bio/hc/en-us/articles/360025758392-Managing-data-with-tables-)
* [How Gen3 stores data](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/explore_data/gen3-discovering-data)
* [Gen3's data dictionary](https://gen3.datastage.io/DD)
* [How Gen3 data interacts with Terra](https://support.terra.bio/hc/en-us/articles/360038087312)

Head on over to [Gen3](https://gen3.datastage.io/) and log in by clicking "Profile" in the top right hand corner. You will need to log in using your NIH eRA Commons ID. When you click on "Exploration" you will see all subjects and studies you have access to. Use filters on the right hand side of the screen to select what you are interested in. For instance, you could limit your search to subjects 

![censored view of Gen3 when you are logged in, with "export to Terra" button highlighted](https://github.com/aofarrel/tutorials/blob/master/gen3_overview_with_text_resized.png?raw=true)

After selecting a group of subjects, click the red "Export To Terra" button to do precisely that. Bear in mind that exporting may take a few minutes and you shouldn't navigate away from the page while this is happening. Once it completes, you will be taken to a Terra page where you can choose which workspace to put your data into.

### DRS: How We Keep Data Safe

Now that you're dealing with controlled-access data, you will notice that links to that data are formatted differently. If you check the JSON from step 2, you'll see that those URLs were in the gs:// form, which is used for interacting with Google Buckets. This time, you are now dealing with drs:// instead. Technically speaking these are URIs  (Uniform Resource Identifiers; a URL is a type of URI). GA4GH uses DRS (Data Repository Service) URIs for controlled access data. For more information, please see [Terra's documentation on GA4GH's Data Repository Service](https://support.terra.bio/hc/en-us/articles/360039330211).

### Gen3's Data Structure
The links at the top of this section should serve as an explanation as to how Gen3 data is stored. But even with that background, it may still look a little odd when imported into Terra, so we wanted to make note of a few things.

At the top you're see "Submitted Aligned Reads." If you scroll across that, you will see a column named "data_format," indicating that these are CRAM files. But where are those files actually? Keep scrolling and you will see "object_id" as a column header, and under that, several drs:// URIs. This is what Terra will be using to locate the files. Thankfully, you don't need to remember these URIs. As with what we did for the 1000 Genomes data above, when running this workflow, we can simply select the data we want from the dropdown and enter "this.object_id" as the `input_cram_file`.

We don't need to input CRAI files for the aligner. However, let's pretend we did for the sake of learning more about Gen3's data structure. CRAI files are considered a child of CRAM files, or in other words, "submitted aligned reads" table (which includes the CRAM files and their metadata) is the parent of "aligned reads index" table (the CRAI files and their metadata). When dealing with Gen3 data, children know their parents, but not vice versa. In other words, the table containing CRAI files also links to the table that contains CRAM files. We can take advantage of this by going back to the drop down menu and selecting "Aligned Reads Index" instead of "Submitted Aligned Reads." For `input_crai_file`, you simply enter that table's link to the CRAI files, ie, "this.object_id." And for `input_cram_file`, the correct entry is "this.submitted_aligned_reads.object_id". Handy, isn't it?

# Running the Aligner on Your Data

Go to this workspace's workflows tab. You will see two buttons. Select the bubble labeled "Process multiple workflows from:", and in the drop down menu, select "Aligned Reads Index". What if you wanted to select less subjects than are in your data table? Click "Select Data" which is located to the right of the dropdown menu. This will open a new menu where you can select precisely which rows you want to run your workflow on. In this case, each row represents a subject.

![how to select individual rows using the checkboxes on the far left side of each row](https://github.com/aofarrel/tutorials/blob/master/resized_selecting_only_some_CRAMs.png?raw=true)

### Setting the Inputs
Like what was indicated in the section detailing Gen3's data structure, your input will depend on what table you select. What's easiest for our purposes is to just select "submitted aligned reads" and use the argument `this.object_id`. You will need to select this table manually.

For more information on setting inputs on Terra, please see [Terra's documentation on the subject.](https://support.terra.bio/hc/en-us/articles/360026521831-Configure-a-workflow-to-process-your-data)

### To Preempt or Not to Preempt

(If you already know what preemptibles are and don't want to use them, you can skip this section, as this template workspace is set up to avoid them by default.)

When computing on Google Cloud, you have the option of using preemptible virtual machines. These virtual machines work essentially the same as what you expect from Google Cloud, but they are significantly cheaper (sometimes less than half the price!). There is a catch, however -- they may shut down in the middle of a task and only exist for 24 hours at most. You can find more information [from Google](https://cloud.google.com/preemptible-vms/), but let's talk about the specifics of this workspace. 

This wasn't really a concern earlier on when running on tiny test CRAM files, as those files generally complete in less than ten minutes. But when running on full-size CRAM files such as those in the 1000 Genomes project, there is a chance that any given step (pre-alignment, alignment, or post-alignment) might get shut down before it completes if it's run on a preemptible VM. This is especially true of the post-align step, which tends to take the longest, often more than 24 hours when running on full-sized CRAM files.

We've broken down some possibilities here so you can balance risk, run time, and cost:


| Setting        | Max # of Tries On Preemptible VM           | Max # of Non-Preemptible Tries  |  Total Max Tries |
| ------------- |:-------------:|:-------------:|  -----:|
| `PostAlign_preemptible_tries`​ = 0 and `PostAlign_max_retries`​ = 1 |  0  | 1 | 1 |
| `PostAlign_preemptible_tries`​ = 3 and `PostAlign_max_retries`​ = 3 |  3  | 3 | 6 |
| You don't set `PostAlign_preemptible_tries`​ nor `PostAlign_max_retries`​, ie, default values are used |  0  | 3 | 3 |

Whenever `preemptible_tries` is a positive integer, the task will exhaust all preemptible tries before trying on the more expensive non-preemptible VM. The same logic applies for `PreAlign_preemptible_tries` and `PreAlign_max_retries`, as well as `Align_preemptible_tries` and `Align_max_retries`. Terra handles all this on its own; you don't have to manually restart any of these tasks as long as the workflow itself is still running.

Once you have decided if you want to change the default behavior for preemptible VMs, press "save" and run your workflow.

### Tracking the Aligner's Progress
With your workflow now running, click on the "JOB HISTORY" tab at the top of this workspace. You will see a table detailing every submission you have made in this workspace. If you've been following this tutorial you'll see two, one for the test data, and one running on the 1000 Genomes data.

Let's click on the top row, which shows the most recently submitted workflow. You will see something like this -- metadata on the top, and below that, a one-row table. If you click on "view" in the leftmost tab of the table (marked with a square below), you will more information about the workflow, including how long it's running certain tasks. In this case, that means pre-alignment, alignment, and post-alignment. This can come in handy of a workflow fails, as this screen will tell you how long each task has run for, how many attempts it had, and (if applicable) any logs created in the process. You can also click "workflow ID" in the leftmost column (circled) to be taken to Google Cloud's own website to explore the bucket, although you can do the same on Terra itself.

Take note of the submission ID and workflow ID, or at least the first few digits of each. They'll come in handy in a moment.

![screenshot of job history page with view, workflow ID, and submission ID circled](https://github.com/aofarrel/tutorials/blob/master/resized_smaller_workflow_history.png?raw=true)

### Examining Output
As the aligner does its thing, it will begin writing files to the disk -- or, rather, to the Google Cloud bucket your workspace is hosted on. You can find this in the "Files" section of your workspace's DATA tab, below anything you have imported.

[//]: # ({Will need to change this if screenshots are retaken with diff IDs})

Remember that workflow ID we took note of earlier? That workflow ID is also the name of the folder that your workflow is writing to. Keep in mind *every time* you run a workflow, a new folder will be created, even if you are just running the same workflow on the same data with the same parameters. In your workspace's file system, your output data for the aligner will be stored in `Files/{submission ID}/TopMedAligner/{workflow ID}/call-PostAlign/`. So, for instance, in the screenshot above my submission ID started with "b17b9937-ff17-46e8-b20b-798dc3d4ebf2" and my workflow ID started with "2368abf3-0e2a-4cd7-b10a-89d4d1f9002d." So my output files are located in `Files / b17b9937-ff17-46e8-b20b-798dc3d4ebf2 / TopMedAligner / 2368abf3-0e2a-4cd7-b10a-89d4d1f9002d / call-PostAlign /`. Of course, these files will only be created once the aligner finishes -- if your workflow is running but hasn't finished yet, the `call-PostAlign` folder might not exist yet.


## Final Notes
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
Workspace author: Ash O'Farrell  
WDL script and bug fixing: Walt Shands  
Dockerized TopMED Aligner: Jonathon LeFaive  
Edits and bug-squashing: Michael Baumann, Beth Sheets  
  
This workspace was completed under the NHLBI BioData Catalyst project.

### Workspace change log 

| Date | Change | Author | 
| -------  | -------- | -------- |
| Feb 4, 2020 | initial draft | Ash |
| Feb 6, 2020 | updated dashboard text | Beth |
| Feb 10, 2020 | gen3, job tracking, output, major revisions | Ash |