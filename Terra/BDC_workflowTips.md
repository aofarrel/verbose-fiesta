# Designing and Running Workflows For Terra: Tips and Tricks

Even if you have written workflows before, it's easy to fall into some pitfalls when working on Terra for the first time. While Terra runs your WDL workflows with Cromwell, just like your local machine, because it's running on Google Cloud, some things are a little different.

- [Designing and Running Workflows For Terra: Tips and Tricks](#designing-and-running-workflows-for-terra--tips-and-tricks)
  * [Helpful Resources](#helpful-resources)
  * [Tips and Tricks: Data Access](#tips-and-tricks--data-access)
    + [Use gs:// inputs, not https://storage.google.com](#use-gs----inputs--not-https---storagegooglecom)
    + [Make sure your credentials are up-to-date](#make-sure-your-credentials-are-up-to-date)
    + [TODO: DRS](#todo--drs)
  * [Tips and Tricks: Runtime Attributes](#tips-and-tricks--runtime-attributes)
    + [Workflow disks must be integers, not floats](#workflow-disks-must-be-integers--not-floats)
    + [sub() does not work in WDL 1.0](#sub---does-not-work-in-wdl-10)
  * [Tips and Tricks: Miscellanous](#tips-and-tricks--miscellanous)
    + [Be careful with comments in command sections](#be-careful-with-comments-in-command-sections)
    + [Forgot which WDL was used for a given workflow run? View it on the command line!](#forgot-which-wdl-was-used-for-a-given-workflow-run--view-it-on-the-command-line-)


## Helpful Resources
* [Terra's WDL documentation resources](https://support.terra.bio/hc/en-us/sections/360007274612-WDL-Documentation)
* [Cloud-based runtime attributes](https://cromwell.readthedocs.io/en/stable/RuntimeAttributes/)
* [Understanding and controlling cloud costs](https://support.terra.bio/hc/en-us/articles/360029748111-Understanding-and-controlling-cloud-costs-)

## Tips and Tricks: Data Access
### Use gs:// inputs, not https://storage.google.com
Terra cannot handle https://storage.google.com inputs, therefore, if one of your input files is in a Google Cloud bucket, use gs:// notation instead.

### Make sure your credentials are up-to-date
If you are having issues accessing controlled-access data on Terra, try refreshing your credentials on Gen3 and/or NIH.

### TODO: DRS

|✅| "gs://topmed_workflow_testing/topmed_aligner/reference_files/hg38/hs38DH.fa" | 
|----------------------|----------------|
|❌| "https://storage.google.com/topmed_workflow_testing/topmed_aligner/reference_files/hg38/hs38DH.fa"  |

## Tips and Tricks: Runtime Attributes
Running WDL locally will ignore a WDL's values for runtime attributes that only apply to the cloud, such as `disks` or `memory`. That means if you had issues with those values, such as using incorrect syntax (see below), those issues will be silent on local runs but will become errors when running on Terra. [See the official spec for pointers on the memory attribute](https://github.com/openwdl/wdl/blob/main/versions/1.0/SPEC.md#memory).

### Workflow disks must be integers, not floats
A runtime attribute commonly used on Terra is `disks` which have a string format and is used for designating a certain amount of storage. Within these strings, you must use integers, not floats. This limitation is technically not one of Terra but rather Google Cloud.

|✅| local-disk 10 HDD| 
|----------------------|----------------|
|❌| local-disk 10.010500148870051 HDD |

Because `size()` returns a float, if you are basing your disk size on the size of your inputs, you will want to use `floor()` or `ceil()`, which will round a float to the previous or next integer respectively.

### sub() does not work in WDL 1.0
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


## Tips and Tricks: Miscellanous
### Be careful with comments in command sections
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

### Forgot which WDL was used for a given workflow run? View it on the command line!
If you are developing a workflow and need to run multiple tests on Terra, you'll probably be updating your workflow a lot. Because Terra's UI shows neither the WDL nor the version number (if the WDL came from the Broad Methods Repo) on your workflow page, it's easy to lose track, especially if you're running multiple tests at once. Thankfully, you can extract the WDL once a workflow has finished, albeit not in the Terra UI.

When you click the "view" button to bring up the job manager, take note of the ID in the top, not to be confused with the workspace-id or submission-id.  

![Screenshot showing the ID of a workflow under the first heading in the Job Manager page](https://raw.githubusercontent.com/aofarrel/verbose-fiesta/master/Terra/Images/BDC_workflowTips.png)

You can use this ID on your local machine's command line to display the WDL on stdout.

`curl -X GET "https://api.firecloud.org/api/workflows/v1/PUT-WORKFLOW-ID-HERE/metadata" -H "accept: application/json" -H "Authorization: Bearer $(gcloud auth print-access-token)" | jq -r '.submittedFiles.workflow'`

Note that you will need to [install gcloud and login with the same Google account that your workspace uses](https://cloud.google.com/sdk/docs/quickstarts), and you will need jq to parse the result. jq can be easily installed on Mac with `brew install jq`

With this quick setup, you'll be able to check the WDL of previously run workflows in a flash. To make this process more efficient, put a comment in the WDL itself explaining its changes.
