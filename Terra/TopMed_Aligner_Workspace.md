# TOPMed Featured Workspace (Feb 4th, Scrapped Completely)
###### tags: `aligner` `archive`
In this workspace, you will be running an alignment workflow as you learn how to use Terra, Dockstore, and Gen3 together as part of BioData Catalyst. This is made for people who are not familiar with any of these platforms, but do note that in order to use TOPMed data from Gen3, you will need access via dbGaP. If you don't have dbGaP access, worry not -- you'll still be able to run this workflow on open-access data we have provided.

# Step 1: Setting Up
First of all, you will need to clone this workspace to your account. You can find the button for that in the far right.

![cloning the workspace](https://raw.githubusercontent.com/aofarrel/tutorials/master/clone_workspace.png)

You will need to connect a billing account to your cloned workspace. That billing account will be charged when you run the workflows in this workspace. For more information on how to avoid runaway costs, see [this article here](https://support.terra.bio/hc/en-us/articles/360029748111-Understanding-and-controlling-cloud-costs-) for an overview, or [this one](https://support.terra.bio/hc/en-us/articles/360029772212-Controlling-Cloud-costs-sample-use-cases) for specific examples.

As you go through this tutorial, you will probably want to keep this page open on a separate tab so you can easily read these directions while navigating on Gen3, Dockstore, and the workflow section of Terra.

# Step 2: Using a JSON From Dockstore ("Walt's Data")
Background info (optional):
* [What is TOPMed?](https://www.nhlbiwgs.org/)
* What is Dockstore? --> LINK TO OUR VERY GOOD AND COOL DOCKSTORE DOCUMENTATION ON BDC'S DOCS

Now that you have copied over this workspace and connected it to a billing account, you'll be able to run the workflows contained within. For now, we will be testing our aligner on open-access data generously provided by TOPMed. Much of TOPMed data is restricted access, but this particular portion is open access. Because we will later on be going through controlled access TOPMed data, for simplicity, we will be calling this open access part "Walt's Data" as the JSON with this data (more on that soon) was complied by Walt Shands at UCSC.

You might have used aligners on the command line before. Usually, this involves putting a long string of variables into the command line, often in a set order. Here on Terra we do things a little bit differently -- you have a GUI to allow you to see which files you're putting in more clearly. You also have the option of uploading a JSON file to fill this out for you. Let's try that now.

In the top tabs of Terra, click on WORKFLOWS and you will see the alignment workflow.

PHOTO: THE WORKFLOWS --> TAKE ON CLEAN WORKSPACE WITH LESS CLUTTER

Select the aligner workflow (1--Aligner) and you will see a github URL after the label "source." Click on that link and it will take you to Dockstore, which is where the workflow was pulled from.

![link to take you to dockstore, which actually will say github but takes you to dockstore](https://github.com/aofarrel/tutorials/blob/master/link_to_dockerstore.png?raw=true)

Now, on Dockstore, click Files, then Test Parameter Files. You will see a JSON file with several google storage links -- this is what we are calling Walt's Data. Download this file using the icon with an arrow pointing down. 

PHOTO: DOWNLOADING THE JSON FROM DOCKSTORE WITH FILES, TEST PARAMETER FILES, AND THE DL ICON ALL HIGHLIGHTED --> PAUSE FOR NOW UNTIL WE KNOW JSON WILL BE ON DOCKSTORE, MIGHT BE ELSEWHERE

You can close Dockstore if you want; you won't need it for the rest of this tutorial.

Now, back to Terra. Click on "upload JSON" and select the JSON you just downloaded from Dockstore. Your fields will magically be filled in on Terra, saving you from having to type them out yourself. Now, click "save," then run your workflow! Because Walt's Data is downsampled, this test should complete in five minutes or less.

![arrow pointing to where the upload json button is](https://github.com/aofarrel/tutorials/blob/master/upload_JSON.png?raw=true!)


# Step 3: Aligning With 1000 Genomes Data
Background info (optional):
* [What is 1000 Genomes?](https://www.internationalgenome.org/about)

So we've started our alignment with test data. That's all well and good, but we used downsampled = data which isn't all that useful for research purposes. What if I want to do an alignment with 1000 Genomes data?

You're in luck, because Terra already hosts 1000 Genomes data.

On the top left next to the Terra logo, you will see three bars. Click on that and you will see library. Select data from the dropdown menu and you will be taken to a collection of open-access data just waiting to be analyzed. Let's select the "1000 Genomes High Coverage presented by NHGRI AnVIL" for our purposes.

![indicator of where to get to Terra's library page](https://raw.githubusercontent.com/aofarrel/tutorials/master/get_to_data.png)

You'll be taken to a workspace for the 1000 Genomes project. Click on the DATA tab at the top and you will be taken to a page that shows more than 2000 samples. We don't need all 2504 samples for our tests, so let's click on "sample (2504)" and just select the first 5 rows. We then download the resulting TSV file to our local machine. You are NOT copying multiple gigabytes worth of data to your computer, just a TSV file that contains links to said data. *Note: If you want to bring costs down, you can use less than five rows, or even just one row. We'll continue the tutorial with five to make sure it's clear how to input arguments when dealing with multiple subjects.*

![downloading five samples of data](https://github.com/aofarrel/tutorials/blob/master/sample_rows.png?raw=true)

Return to this workspace's data section and upload the TSV file. Now you have the links necessary to use this 1000 Genomes data.

Now, go to this workspace's workflows. You will see two buttons. Select the bubble labeled "Process multiple workflows from:", and in the drop down menu, select samples. This means you are selecting the 5 samples you choose from the 1000 Genomes data.

What if you wanted to select less CRAMs than are in your TSV? Click "Select Data" which is located to the right of the dropdown menu. This will open a new menu where you can select precisely which rows you want to run your workflow on. In this case, each row represents a subject.
![how to select individual rows using the checkboxes on the far left side of each row](https://github.com/aofarrel/tutorials/blob/master/selecting_only_some_CRAMs_now_with_arrows.png?raw=true)

Now that have selected your CRAMs, make sure to look over the arguments that you are running this program on. You'll need to set `input_cram_file` to `this.cram` in order for it to actually use the data in the table you selected -- specifically, the CRAM column. This syntax can be used to select other column names too, but all we need is the CRAM files for this workflow.
![pointing out where to type "this.cram" in the workflow page before running it](https://github.com/aofarrel/tutorials/blob/master/thisdotcram.png?raw=true)

You might be wondering why we are bothering with TSV files rather than just moving around the data itself. The problem is these are full-size CRAM files we are dealing with now, with each one being over ten gigabytes in size. Nothing is stopping you from downloading several gigabytes worth of CRAM files and re-uploading to their workspace's data section, but... it's not recommended. Instead, by using TSV files, we are instead telling Terra where the data is actually hosted (the Google Bucket used for 1000 Genomes data). Just like the JSON we used earlier, we are simply pointing to a file somewhere out there on Google's servers rather than trying to download/upload massive files to our local machine. This is one of the advantages of using Terra -- it's easy to find and use open access data.


### To Preempt or Not to Preempt

When computing on Google Cloud, you have the option of using preemptible virtual machines. These virtual machines work essentially the same as what you expect from Google Cloud, but they are significantly cheaper (sometimes less than half the price!). There is a catch, however -- they may shut down in the middle of a task and only exist for 24 hours at most. You can find more information [from Google](https://cloud.google.com/preemptible-vms/), but let's talk about the specifics of this workspace. 

This wasn't really a concern earlier on when running on tiny test CRAM files, as those files generally complete in less than ten minutes. But when running on full-size CRAM files such as those in the 1000 Genomes project, there is a chance that any given step (pre-alignment, alignment, or post-alignment) might get shut down before it completes if it's run on a preemptible VM. This is especially true of the Post-Align step, which tends to take the longest, often more than 24 hours when running on full-sized CRAM files.

We've broken down some possibilities here so you can balance risk, run time, and cost:


| Setting        | Max # of Tries On Preemptible VM           | Max # of Non-Preemptible Tries  |  Total Max Tries |
| ------------- |:-------------:|:-------------:|  -----:|
| `PostAlign_preemptible_tries`​ = 0 and `PostAlign_max_retries`​ = 1 |  0  | 1 | 1 |
| `PostAlign_preemptible_tries`​ = 3 and `PostAlign_max_retries`​ = 3 |  3  | 3 | 6 |
| You don't set `PostAlign_preemptible_tries`​ nor `PostAlign_max_retries`​, ie, default values are used |  0  | 3 | 3 |

Whenever `preemptible_tries` is a positive integer, the task will exhaust all preemptible tries before trying on the more expensive non-preemptible VM. The same logic applies for `PreAlign_preemptible_tries` and `PreAlign_max_retries`, as well as `Align_preemptible_tries` and `Align_max_retries`, except that unlike Post-Align the default behavior is to attempt these steps on preemptible VMs three times rather than not bothering with preemptibles at all, as these steps usually complete in under 24 hours. Terra handles all this on its own; you don't have to manually restart any of these tasks as long as the workflow itself is still running.

# Step 5: Aligning: Data From Gen3
Note: Because this involves controlled access data, I will be blurring out lots of information in the screenshots that follow.

Head on over to [Gen3](https://gen3.datastage.io/) and log in by clicking "Profile" in the top right hand corner. You will need to log in using either a Google ID or with you NIH Commons ID. Bear in mind that these logins might have access to different projects. (For instance, as I am a developer, I have access to more data than I do as a researcher because developers need access to the data they're writing projects for, but I am not allowed perform original research on data that I do not have researcher access to. If you are a researcher this likely doesn't apply to you and you'll just log in with NIH as you normally do.)
# Step 5: Aligning: BYOD
We've now gone over how to align small amounts of test data from TOPMed ("Walt's Data"), how align 1000 Genomes data, and how to align data from Gen3. We now leave you with some final notes if you want to align your own data using this workflow.

------
**If Your Data Was/Will Be Aligned To Something That Isn't HG38**

Something that may be easy to miss in the aligner's documentation is the following note:
> The CRAM to be realigned may have been aligned with a different reference genome than what will be used in the alignment step. The pre-align step must use the reference genome that the CRAM was originally aligned with to convert the CRAM to a SAM.

There's two important things to take away from this:
1. If what you already aligned your data to (remember, CRAM/SAM/BAM files are already aligned!) does not match the reference genome you are aligning to with this workflow, you must include what you aligned them to as the argument for `PreAlign_reference_genome` and its associated index as the argument for `PreAlign_reference_genome_index`. For instance, if your CRAM files were generated using HG19 as your reference and you are using this workflow to align them HG38, you must set HG38 as your argument for `ref_fasta` and HG19 as `PreAlign_reference_genome`.
2. TOPMed data is aligned to HG38. If you are running this workflow to compare your own data to TOPMed data, you must align to HG38.

------