# TOPMed Aligner (Template Workspace Version)
###### tags: `aligner`

In this workspace, you will be running the TOPMed alignment workflow as you learn how to use the BioData Catalyst platform powered by Terra, Dockstore, and Gen3. This is a beginner-oriented tutorial walks you through how each of these platforms interact with one another.

## Data Models Covered in this Tutorial
One aim of this tutorial is to help you learn the TOPMed data model and how to interact with it. This can help you bring your own data to the BioData Catalyst platform and compare it with the TOPMed data that is currently hosted in this system.

You will learn how to import TOPMed data from Gen3, which uses a more complex graph-based data model than the data model used for what's already hosted on Terra, such [as Terra's version of the 1000 Genomes](https://app.terra.bio/#library/datasets/public/1000%20Genomes/data-explorer). However, in order to use TOPMed data from Gen3, you must be approved to use TOPMed data via [dbGaP](https://www.ncbi.nlm.nih.gov/books/NBK99225/). If you don't have dbGaP access, worry not -- you'll still be able to run this workflow on 1000 Genomes Data, as long as you have an NIH credential such as [eRA Commons](https://era.nih.gov/faqs.htm#II) that can get you into Gen3.

## Setting Up
Because Gen3 hosts controlled-access data, you will need to set up your Terra account for the linkage to Gen3 to work properly. Please see [Terra's documentation on the subject](https://support.terra.bio/hc/en-us/articles/360037648172-Accessing-TCGA-Controlled-Access-workspaces-in-Terra).

Also, you should set up an Authorization Domain to protect your work. This will prevent you from inadvertently sharing data that shouldn't be shared. Learn more in this [article](https://support.terra.bio/hc/en-us/articles/360039415171).

# Using Gen3
Background info (optional, but you do need at least a basic familiarity with how Gen3 stores data):
* [How Terra stores data in tables](https://support.terra.bio/hc/en-us/articles/360025758392-Managing-data-with-tables-)
* [How Gen3 stores data](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/explore_data/gen3-discovering-data)
* [Gen3's data dictionary](https://gen3.biodatacatalyst.nhlbi.nih.gov/DD)

[//]: # (NOT LIVE YET: * How Gen3 data interacts with Terra https://support.terra.bio/hc/en-us/articles/360038087312)

Head on over to [Gen3](https://gen3.biodatacatalyst.nhlbi.nih.gov/) and log in by clicking "Profile" in the top right hand corner. You will need to log in using your NIH eRA Commons ID. When you click on "Exploration" you will see all subjects and studies you have access to. Use filters on the right hand side of the screen to select what you are interested in. For instance, you could limit your search to subjects with a particular blood pressure. If you don't have dpGaP access to any TOPMed studies, you can use 1000 Genomes data, as it is open access.

#### Selecting by study/project
If you're interested in a particular study, click the "subject" tab on the leftmost toolbar. You will see a dropdown menu to select by project ID. In addition to showing the list of studies available, they will also display consent code shorthands in this menu.

![censored view of Gen3 when you are logged in, with "export to Terra" button highlighted](https://github.com/aofarrel/tutorials/blob/master/gen3_overview_with_text_resized.png?raw=true)

You also have the option of selecting by diagnosis or certain phenotypic traits.

#### Importing to Terra
After selecting a group of subjects, click the red "Export To Terra" button to do precisely that. Bear in mind that exporting may take a few minutes and you shouldn't navigate away from the page while this is happening. Once it completes, you will be taken to a Terra page where you can choose which workspace to put your data into. From here, you should select "template workspace" and import into the TOPMed aligner workspace.

![screenshot showing the workspace import, with the topmost selection on the right annotated with a circle as it's the option that allows us to import to a template workspace](https://raw.githubusercontent.com/aofarrel/tutorials/master/bdc_template_import_resized.png)

What happens next? A copy of this exact workspace will be created, with your newly imported data inside. It will take a few minutes for the data to fully import, but once it does, you'll see the workspace has been populated with several data tables.

One of the data tables you'll see is called "Submitted Aligned Reads." If you scroll across that in Terra's UI, you will see a column named "data_format," indicating that these are CRAM files. But where are those files actually? Keep scrolling and you will see "object_id" as a column header, and under that, several drs:// URIs. This is what Terra will be using to locate the files. Thankfully, you don't need to remember these URIs. When running a workflow, if we want to run a WDL on this data, we can reference that object_id column in order to enter dozens (hundreds, even) of URIs into a workflow with just a few clicks.

# Running the Aligner
The links at the top of this section should serve as an explanation as to how Gen3 data is stored. But even with that background, it may still look a little odd when imported into Terra, so let's walk through how to use these tables. If you want a more complete explanation of how these tables relate to each other, please see the optional section towards the end of this workspace.

Go to this workspace's workflows tab, and you will see a WDL has already been imported from DockStore for you to play with: A WDL-ized verison of the aligner used on all TOPMed data. Select it.

![the two buttons in your workspace's data section, with the leftmost allowing you to make a new workflow, and the righmost one being for the TOPMed aligner workflow](https://raw.githubusercontent.com/aofarrel/tutorials/master/aligner_workflow_resized.png)

You will see two buttons. Select the bubble labeled "Run workflow(s) with inputs defined by data table", and in the drop down menu, select "Submitted Aligned Reads". 

What if you wanted to select less subjects than are in your data table? Click "Select Data" which is located to the right of the Step 2 heading. This will open a new menu where you can select precisely which rows you want to run your workflow on. In this case, each row represents a subject.

![screenshot showing the workflow input page on Terra, as described in the text proceeding this image](https://raw.githubusercontent.com/aofarrel/tutorials/master/submitted%20aligned%20reads%20redo.png)

Below this, you will see several arguments that can be used for this workflow. If it's not already filled out, type out `this.object_id` as the `input_cram_file`.

But what is "this"? In Terra, we can use "this" to represent the data table that we selected from the drop down menu. So, in this case, "this" refers to the Submitted Aligned Reads data table. As for ".object_id", that part means that Terra is looking at the column called object_id. 


For more information on using workflow inputs on Terra, please see [Terra's documentation on setting inputs.](https://support.terra.bio/hc/en-us/articles/360026521831-Configure-a-workflow-to-process-your-data)

### To Preempt or Not to Preempt?

*If you already know what preemptibles are and don't want to use them, you can skip this section, as this template workspace is set up to avoid them by default.*

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
With your workflow now running, click on the "JOB HISTORY" tab at the top of this workspace. You will see a table detailing every submission you have made in your workspace.

Let's click on the top row, which shows the most recently submitted workflow. You will see something like this -- metadata on the top, and below that, a one-row table. If you click on "view" in the leftmost tab of the table (marked with a square below), you will more information about the workflow, including how long it's running certain tasks. In this case, that means pre-alignment, alignment, and post-alignment. This can come in handy of a workflow fails, as this screen will tell you how long each task has run for, how many attempts it had, and (if applicable) any logs created in the process. You can also click "workflow ID" in the leftmost column (circled) to be taken to Google Cloud's own website to explore the bucket, although you can do the same on Terra itself.

Take note of the submission ID and workflow ID, or at least the first few digits of each. They'll come in handy in a moment.

![screenshot of job history page with view, workflow ID, and submission ID circled](https://github.com/aofarrel/tutorials/blob/master/resized_smaller_workflow_history.png?raw=true)

### Examining Output
As the aligner does its thing, it will begin writing files to the Google Cloud bucket your workspace is hosted on. You can find this in the "Files" section of your workspace's DATA tab. Note that this "Files" section will be the lefthand side below all of the data tables you imported from Gen3, so you'll probably have to scroll a bit to see it.

[//]: # ({Will need to change this if screenshots are retaken with diff IDs})

Remember that workflow ID we took note of earlier? That workflow ID is also the name of the folder that your workflow is writing to. Keep in mind *every time* you run a workflow, a new folder will be created, even if you are just running the same workflow on the same data with the same parameters. In your workspace's file system, your output data for the aligner will be stored in `Files/{submission ID}/TopMedAligner/{workflow ID}/call-PostAlign/`. So, for instance, in the screenshot above my submission ID started with "b17b9937-ff17-46e8-b20b-798dc3d4ebf2" and my workflow ID started with "2368abf3-0e2a-4cd7-b10a-89d4d1f9002d." So my output files are located in `Files / b17b9937-ff17-46e8-b20b-798dc3d4ebf2 / TopMedAligner / 2368abf3-0e2a-4cd7-b10a-89d4d1f9002d / call-PostAlign /`. Of course, these files will only be created once the aligner finishes -- if your workflow is running but hasn't finished yet, the `call-PostAlign` folder might not exist yet.

# Running on More Than One Data Table
### This is optional to run the aligner included in this workspace, however, certain other workflows may require you to draw upon more than one table, so this is good information to learn.
Go back to the workflow tabs and again select the aligner. However, this time, select "Aligned Reads Index" instead of "Submitted Aligned Reads". Yes, this is a table of CRAI files, not CRAM files -- but like the CRAM table, it too links to its files using DRS URIs in the object_id column. Don't worry, we'll get back to the CRAMs in a moment.

![screenshot of Terra's workflow UI page with the submitted aligned reads index table selected via dropdown menu](https://raw.githubusercontent.com/aofarrel/tutorials/master/aligned%20reads%20index%20redo.png)https://raw.githubusercontent.com/aofarrel/tutorials/master/submitted_aligned_reads_index_small.png)

Like what was indicated in the section detailing Gen3's data structure, your input will depend on what table you select. In our case, we selected  Submitted Aligned Reads Index. So that means that now anything that says "this" for an input will be looking at the Submitted Aligned Reads Index table.

In this version of the TOPMed aligner, CRAI files optional, but for the sake of learning more about Gen3's data structure we will be using them. In Gen3's data structure, CRAI files are considered a child of CRAM files, or in other words, "submitted aligned reads" table (which includes the CRAM files and their metadata) is the parent of "aligned reads index" table (the CRAI files and their metadata). When dealing with Gen3 data, children know their parents, but not vice versa. **In summary, the table containing CRAI files also links to the table that contains CRAM files, but not vice versa.** In this particular example, that link is stored in the CRAI table as a column named submitted_aligned_reads. Note that it does not link to the table overall, but rather a given row of that table: Each row on the CRAI table points to the row of the CRAM table that its associated with. This can be a bit confusing to wrap your head around, so feel free to click through your data tables on Terra or review Gen3's documentation if you're getting a headache at this point.

Why does this matter? Because you can use this to effectively use two different data tables in your analysis, even though at first glance it looks like you can only select one in the drop down menu. You simply enter that table's link to the CRAI files, ie, `this.object_id` as the input for `input_crai_file`. (You will have to scroll to find `input_crai_file` as Terra puts optional arguments below all required arguments.) And for `input_cram_file`, the correct entry is `this.submitted_aligned_reads.object_id`. Handy, isn't it?


# Final Notes
We've now gone over how to set up the TOPMed alignment workflow to work with the Gen3 graph-model . We now leave you with some final notes if you want to align your data from other sources using this workflow.
* [How to run WDLs from Dockstore](https://bdcatalyst.gitbook.io/biodata-catalyst-documentation/community_tools/dockstore-example) -- useful if you want to preform further analysis on your newly aligned data
* [TOPMed Variant Caller workflow on Dockstore](https://dockstore.org/workflows/github.com/DataBiosphere/topmed-workflows/UM_variant_caller_wdl:feature/checker-optional-crai?tab=info)

### If Your Data Was/Will Be Aligned to Something that Isn't HG38

Something that may be easy to miss in the aligner's documentation is the following note:
> The CRAM to be realigned may have been aligned with a different reference genome than what will be used in the alignment step. The pre-align step must use the reference genome that the CRAM was originally aligned with to convert the CRAM to a SAM.

There's two important things to take away from this:
1. If what you already aligned your data to (remember, CRAM/SAM/BAM files are already aligned!) does not match the reference genome you are aligning to with this workflow, you must include what you aligned them to as the argument for `PreAlign_reference_genome` and its associated index as the argument for `PreAlign_reference_genome_index`. For instance, if your CRAM files were generated using HG19 as your reference and you are using this workflow to align them HG38, you must set HG38 as your argument for `ref_fasta` and HG19 as `PreAlign_reference_genome`.
2. TOPMed data is aligned to HG38. If you are running this workflow to compare your own data to TOPMed data, you must align to HG38.

### DRS: How We Keep Data Safe
Like we hinted at earlier in this workspace, if you look at the `object_id` column of your datatables, you will notice that instead of https:// or gs://, you'll see drs:// instead. Technically speaking these are URIs  (Uniform Resource Identifiers; a URL is a type of URI). GA4GH uses DRS (Data Repository Service) URIs for controlled access data. For more information, please see [Terra's documentation on GA4GH's Data Repository Service](https://support.terra.bio/hc/en-us/articles/360039330211).


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
| Mar 27, 2020 | major reorganization, removal of unneeded info, better data structure explanation | Ash |