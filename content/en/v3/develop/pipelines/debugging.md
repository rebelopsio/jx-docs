---
title: Debugging
linktitle: Debugging
type: docs
description: Debugging pipelines in Jenkins X
weight: 410
---

Here is a demo which shows how to debug pipelines:

<iframe width="850" height="500" src="https://www.youtube.com/embed/QqTaclB6-oI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Prerequisites 

Make sure [your cluster is upgraded to the latest version stream](/v3/admin/setup/upgrades/cluster/). 

If you intend to use the `jx` in the below examples make sure you [upgrade the CLI too](/v3/admin/setup/upgrades/cli/)


### Enable a breakpoint

To enable a breakpoint you can: 

* use the [Lens UI](/v3/develop/ui/lens/) as shown in the above video by:
  * right click on a `Pipeline` action menu 
  * select `Breakpoint -> Add`
* you can use the [jx pipeline debug](https://jenkins-x.io/v3/develop/reference/jx/pipeline/debug/) command then select the pipeline to add/remove a breakpoint.
                                      
### Viewing breakpoints

You can view breakpoints in the [Lens UI](/v3/develop/ui/lens/) in the `Breakpoints` tab or via:

```bash 
kubectl get lighthousebreakpoints

# you can use the short name:
kubectl get lhbp
```


### Using a breakpoint

Once you have set a breakpoint defined for a particular Pipeline you need to trigger the pipeline. e.g. perform a git commit on the git branch to trigger a new pipeline to execute.

The pipeline will execute as normal; you'll be able to view it execute via:

* [Lens UI](/v3/develop/ui/lens/)
* run [jx pipeline grid](https://jenkins-x.io/v3/develop/reference/jx/pipeline/grid/) to watch pipelines run and select the one you wish to view the log 
* run [jx pipeline log](https://jenkins-x.io/v3/develop/reference/jx/pipeline/log/) to watch the log of a specific pipeline

### Opening a shell

Once your breakpoint is reached the pipeline pod will pause, waiting to continue.

At this point you can then open a shell inside the container.

The easiest way to do this is via the [Lens UI](/v3/develop/ui/lens/), click on the Pipeline action menu then `Shell` -> `latest step` and a shell will open.

Otherwise you can use:
 
```bash 
kubectl exec -it -c $name-of-container $name-of-pod (sh | bash | ash)
```

### Continuing after the breakpoint
          
If you wish to continue the execution of a pipeline there are [multiple scripts you can run inside the shell](https://github.com/tektoncd/pipeline/blob/main/docs/debug.md#debug-scripts) you can run inside the shell in the pipeline to tell the pipeline to continue:

| Script | Description |
| --- | --- |
| `/tekton/debug/scripts/debug-continue` | Mark the step as completed with success by writing to `/tekton/tools` so that the pipeline continues executing |
| `/tekton/debug/scripts/debug-fail-continue` | Mark the step as completed with failure by writing to `/tekton/tools` which can lead to the pipeline terminating |

### Removing breakpoints

There are a few ways to delete breakpoints. 
     
You can run [jx pipeline debug](https://jenkins-x.io/v3/develop/reference/jx/pipeline/debug/) and toggle off any existing breakpoints.

You can use the `Breakpoints` tab in [Lens UI](/v3/develop/ui/lens/) then click the breakpoints action menu then `Remove`

Or find the one you want via: 

```bash 
kubectl get lhbp
kubectl delete lhbp whatever-the-name-is
```
