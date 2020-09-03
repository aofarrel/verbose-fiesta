# Designing and Running Workflows For Terra: Tips And Tricks

While Terra runs your WDL workflows with Cromwell, just like your local machine, some things are a little different in practice. We've compiled a list of general advice to those who are new to writing workflows for Terra's compute envirnonment and to aid with troubleshooting. This assumes some familarity with WDL itself, so those new to the world of WDL may benefit more from the spec or other resources in this BYOT document.

- [Helpful Resources](#helpful-resources)
- [Tips and Tricks: Data Access](#tips-and-tricks-data-access)
  * [General DRS tips](#general-drs-tips)
  * [Use gs:// inputs](#use-gs-inputs)
  * [Make sure your credentials are current](#make-sure-your-credentials-are-current)
- [Tips and Tricks: Runtime Attributes](#tips-and-tricks-runtime-attributes)
  * [Cromwell can handle preemptible VM interruptions for you](#Cromwell-can-handle-preemptible-VM-interruptions-for-you)
  * [Disks attribute must use integers](#disks-attribute-must-use-integers)
  * [Make floats integers with ceil() instead of sub()](#make-floats-integers-with-ceil-instead-of-sub)
- [Tips and Tricks: Efficiency](#tips-and-tricks-efficiency)
  * [Saving money with preemptibles: Risks and benefits](#saving-money-with-preemptibles-risks-and-benefits)
- [Tips and Tricks: Miscellanous](#tips-and-tricks-miscellanous)
  * [Be careful with comments](#be-careful-with-comments)
  * [Use Google Cloud CLI to view the WDL for any given Terra run](#use-google-cloud-cli-to-view-the-wdl-for-any-given-terra-run)


## Helpful Resources
* [Terra's WDL documentation resources](https://support.terra.bio/hc/en-us/sections/360007274612-WDL-Documentation)
* [Cloud-based runtime attributes](https://cromwell.readthedocs.io/en/stable/RuntimeAttributes/)
* [Understanding and controlling cloud costs](https://support.terra.bio/hc/en-us/articles/360029748111-Understanding-and-controlling-cloud-costs-)

## Tips and Tricks: Data Access

### General DRS tips
DRS URIs are used by TOPMed files on the Gen3 system. DRS not only helps handle access handles, it also provides a unique identifier to each file. However, if your workflow was developed with gs:// URIs in mind, you may have to make some changes to your WDL.

When working with DRS URIs, sometimes you will want to have your inputs be considered strings rather than file paths. Cromwell will automatically resolve DRS URIs for you (assuming your credentials are up-to-date, see below) but depending on how your inputs are set up, some changes might be necessary, such as if you're using symlinks.

[This diff on GitHub](https://github.com/DataBiosphere/topmed-workflow-variant-calling/pull/4/files) shows the changes that were needed to make an already existing WDL work with DRS URIs on Terra. Although it is a somewhat complicated example, it may be a helpful template for your own changes.

### Use gs:// inputs
Terra cannot handle https://storage.google.com inputs, therefore, if one of your input files is in a Google Cloud bucket, use gs:// notation instead.

|✅| "gs://topmed_workflow_testing/topmed_aligner/reference_files/hg38/hs38DH.fa" | 
|----------------------|----------------|
|❌| "https://storage.google.com/topmed_workflow_testing/topmed_aligner/reference_files/hg38/hs38DH.fa"  |

### Make sure your credentials are current
If you are having issues accessing controlled-access data on Terra, try refreshing your credentials. See Terra support on [linking your eRA commons and University of Chicago DCP framework](https://support.terra.bio/hc/en-us/articles/360037648172-Accessing-TCGA-Controlled-Access-workspaces-in-Terra).

## Tips and Tricks: Runtime Attributes
Running WDL locally will ignore a WDL's values for runtime attributes that only apply to the cloud, such as `disks` or `memory`. That means if you had issues with those values, such as using incorrect syntax (see below), those issues will be silent on local runs but will become errors when running on Terra. See the official spec for [pointers on the memory attribute](https://github.com/openwdl/wdl/blob/main/versions/1.0/SPEC.md#memory).

### Cromwell can handle preemptible VM interruptions for you
If you include the runtime attribute `preemptible` in your WDL, you can specify the number of maximum number of times Terra will request a preemptible machine for a task before defaulting back to a non-preemptible machine. For instance, if your set `preemptible: 2`, your workflow will attempt a preembtible at first, and if that machine gets preempted, it will try again with a preemptible again, and if that second try is preempted, then it will use a non-preemptible. For advice on weighing the costs and benefits of preemptibles, see [Saving money with preemptibles: Risks and benefits](#saving-money-with-preemptibles-risks-and-benefits).

### Disks attribute must use integers
A runtime attribute commonly used on Terra is `disks` which have a string format and is used for designating a certain amount of storage. Within these strings, you must use integers, not floats. This limitation is technically not one of Terra but rather Google Cloud.

|✅| local-disk 10 HDD| 
|----------------------|----------------|
|❌| local-disk 10.010500148870051 HDD |

Because `size()` returns a float, if you are basing your disk size on the size of your inputs, you will want to use `floor()` or `ceil()`, which will round a float to the previous or next integer respectively.

### Make floats integers with ceil() instead of sub()
If your WDL does not specify `version 1.0` at the top, the following is a valid disk string:
`disks: "local-disk " + sub(disk_size, "\\..*", "") + " HDD"`
But under WDL 1.0 rules, this will not pass womtool. Although you might be tempted to use something like this, which will pass womtool...
`disks: disk_size`
...that will not run on Terra. As indicated above, disk strings must be in the format of either `local-disk SIZE TYPE` or `/mount/point SIZE TYPE`, where SIZE is an integer.

|✅|`disks: "local-disk " + disk_size + " HDD"`| 
|----------------------|----------------|
|❌ if WDL 1.0, ✅ otherwise| `disks: "local-disk " + sub(disk_size, "\\..*", "") + " HDD"` |
|❌ | `disks: disk_size` |

The same logic applies for memory.
|✅|`memory: memory + "GB"`| 
|----------------------|----------------|
|❌ if WDL 1.0, ✅ otherwise| `memory: sub(memory, "\\..*", "") + " GB"` |
|❌ | `memory: memory` |

### Calculate size with strings
If you have a task that is used to calculate disk size, you can simply pass it a string of the file name instead of the actual file. This will allow Terra to query the size of the file without actually downloading it to the VM.

## Tips and Tricks: Efficiency
### Saving money with preemptibles: Risks and benefits
Preemptible VM instances are an easy way to save money when computing on Google Cloud, including Terra. You can see [Google's information about them here](https://cloud.google.com/compute/docs/instances/preemptible), but what that won't tell you is when you should or shouldn't use them when designing your workflows.

A preemptible VM instance is a fraction of the cost of a non-preemptible VM instance, so they're an attractive prospect for those who want to reduce costs. However, the inherent risk of using a preemptible VM is that it can be shut down at any time ("be preempted"). This risk appears to increase over time, and Google will shut down any preemptible that has been running for more than 24 hours. It is important to note that preemptibles are defined for individual tasks, not the overall workflow -- that way, if your first task succeeds, but your second one is preempted, you will not have to re-run your first task. To restate: Preemptibles might result in you having to re-run a task, but shouldn't result in having to re-run an entire workflow.

So, generally speaking, the longer you expect a task to run, the more "dangerous" it is to run it on a preemptible, and if you expect a task to take more than 24 hours then you definitely should not run it on a preemptible. Beyond that, things tend to fall into guidelines rather than hard and fast rules.

Generally speaking, preemptibles are a great option if expect a task to take an hour or less. Most of the time, you can expect a preemptible to last at least four hours, so tasks in that timeframe are good candidates too. Tasks expected to take between about 5 and 20 hours depend on whether you can afford to wait double the expected the workflow time, as it is entirely possible that your 20 hour task will be preempted at 19.5 hours and have to start over from the beginning of that task. Cost should play a role in your consideration too: The cost of running a task once on a preempted preemptible and once on a non-preemptible will of course be more expensive then running once on a non-preemptible. However, the savings of using preemptibles are so great that the cost of running a task twice on preemptibles (such as if you set `preemptible: 2` and it is interrupted the first time but not the second) will usually be less than running it once on a non-preemptible.

A good idea is to allow the user to enable or disable preemptibles for each task, which can save money on test runs on smaller datasets that are take less time to compute and therefore are less likely to be preempted.

## Tips and Tricks: Miscellanous
### Be careful with comments
Because command sections of a WDL can interpret BASH commands, and BASH commands sometimes make use of the # symbol, sometimes Cromwell misinterprets comments as syntax. This usually only happens if there are special characters in the comment; alphanumerics should work fine.

✅ This will work:
`command <<<`
`    echo foo`
`    #this is a valid comment`
`>>>`

❌ This will fail womtool:
`command <<<`
`    echo foo`
`    #using <<<this syntax>>> for your command section is ~{very cool}!`
`>>>`

### Use Google Cloud CLI to view the WDL for any given Terra run
If you are developing a workflow and need to run multiple tests on Terra, you'll probably be updating your workflow a lot. Because Terra's UI shows neither the WDL nor the version number (if the WDL came from the Broad Methods Repo) on your workflow page, it's easy to lose track, especially if you're running multiple tests at once. Thankfully, you can extract the WDL once a workflow has finished, albeit not in the Terra UI.

When you click the "view" button to bring up the job manager, take note of the ID in the top, not to be confused with the workspace-id or submission-id.  

![Screenshot showing the ID of a workflow under the first heading in the Job Manager page](https://raw.githubusercontent.com/aofarrel/verbose-fiesta/master/Terra/Images/BDC_workflowTips.png)

You can use this ID on your local machine's command line to display the WDL on stdout.

`curl -X GET "https://api.firecloud.org/api/workflows/v1/PUT-WORKFLOW-ID-HERE/metadata" -H "accept: application/json" -H "Authorization: Bearer $(gcloud auth print-access-token)" | jq -r '.submittedFiles.workflow'`

Note that you will need to [install gcloud and login with the same Google account that your workspace uses](https://cloud.google.com/sdk/docs/quickstarts), and you will need jq to parse the result. jq can be easily installed on Mac with `brew install jq`

With this quick setup, you'll be able to check the WDL of previously run workflows in a flash. To make this process more efficient, put a comment in the WDL itself explaining its changes.
