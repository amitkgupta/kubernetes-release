# kubernetes-release

This project is an experiment to see if open source [Kubernetes](http://kubernetes.io/) and supporting software from CoreOS (e.g. flannel) can be deployed and managed in a [production-suitable manner](http://bosh.io/), not just [hacked together](https://github.com/kubernetes/kubernetes/tree/master/cluster).

It is based on the [Manual Installation Guide](https://coreos.com/kubernetes/docs/latest/getting-started.html) for deploying Kubernetes on CoreOS, but does not use CoreOS.

## Disclaimer

This is not presently a production-ready release. This is a work in progress. It is suitable for experimentation and may become unsupported in the future.

## Usage

This project packages up Kubernetes as a BOSH release, to be deployed in any configuration, on several possible supported IaaSes by a BOSH director.

#### A Brief Preamble on BOSH

> **BOSH** is an open source tool for release engineering, deployment, lifecycle management, and monitoring of distributed systems.

> A **BOSH release** is a versioned collection of configuration properties, configuration templates, start up scripts, source code, binary artifacts, and anything else required to build and deploy software in a reproducible way.

> The **BOSH Director** is deployed to orchestrate the deployments of other software.

For more on BOSH, visit [bosh.io](http://bosh.io/) and check out [the docs](http://bosh.io/docs), especially [What is BOSH?](http://bosh.io/docs/about.html) and [What Problems Does BOSH Solve?](http://bosh.io/docs/problems.html)

#### Step-by-Step Instructions

1. Deploy a BOSH Director:
  * For simple hacking, the easiest thing to do is `vagrant up` a [BOSH-Lite](https://github.com/cloudfoundry/bosh-lite) which deploys do Linux containers instead of IaaS VMs. (In this case, you'll have a Vagrant VM, with nodes in the Kubernetes clusters running in Linux containers inside the VM, and those nodes will be running Docker inside the Linux containers, which wlil then run your pods inside Docker containers!)
  * For a real deployment, [use `bosh-init`](https://bosh.io/docs/using-bosh-init.html).
1. Get the [`bosh` CLI](https://bosh.io/docs/bosh-cli.html) and [target your Director](https://bosh.io/docs/sysadmin-commands.html#director).
1. Create a [BOSH deployment manifest](https://bosh.io/docs/deployment-manifest.html) referencing this release, [etcd release](https://bosh.io/releases/github.com/cloudfoundry-incubator/etcd-release?all=1) and an IaaS-appropriate [BOSH stemcell](https://bosh.io/stemcells):
  * If deploying to BOSH-Lite, you can mostly crib off the [BOSH-Lite example manifest](example_deployments/bosh-lite/kubernetes.yml).
1. Run `bosh -d ${PATH_TO_MANIFEST} deploy`.

## Known Issues

**"Error updating node status" in `kubelet_master` logs**:

In `kubelet_master` error logs there appears to be a lot of the following type of output:

```
[2015-11-29 05:09:20+0000] E1129 05:09:20.092267    4947 kubelet.go:2284] Error updating node status, will retry: error getting node 10.244.5.2: node 10.244.5.2 not found
[2015-11-29 05:09:20+0000] E1129 05:09:20.093377    4947 kubelet.go:2284] Error updating node status, will retry: error getting node 10.244.5.2: node 10.244.5.2 not found
[2015-11-29 05:09:20+0000] E1129 05:09:20.094310    4947 kubelet.go:2284] Error updating node status, will retry: error getting node 10.244.5.2: node 10.244.5.2 not found
[2015-11-29 05:09:20+0000] E1129 05:09:20.095560    4947 kubelet.go:2284] Error updating node status, will retry: error getting node 10.244.5.2: node 10.244.5.2 not found
[2015-11-29 05:09:20+0000] E1129 05:09:20.096722    4947 kubelet.go:2284] Error updating node status, will retry: error getting node 10.244.5.2: node 10.244.5.2 not found
[2015-11-29 05:09:20+0000] E1129 05:09:20.096742    4947 kubelet.go:918] Unable to update node status: update node status exceeds retry count
```

This is likely a manifestation of the issue mentioned [here](https://coreos.com/kubernetes/docs/latest/deploy-master.html):

> Note that the kubelet running on a master node may log repeated attempts to post its status to the API server. These warnings are expected behavior and can be ignored.

**"Failed to acquire subnet: out of subnets" in `flannel` logs**:

It's not clear if this is actually breaking anything, but simply providing a `pod_network` larger than `/23` should work around the issue.

**Docker puts stuff in `/var/run/docker/netns` and `/var/lib/docker/network`**:

There doesn't seem to be a way to tell the `daemon` command to use different directories, like you can for `--graph` and `--execroot`.

See [`daemon` command documentation](https://docs.docker.com/engine/reference/commandline/daemon/).

## Contributing

**Everyone** is encouraged to help improve this project.

Here are some ways *you* can contribute:

* by using it
* by suggesting new features
* by reporting and commenting on [issues](https://github.com/amitkgupta/kubernetes-release/issues)
* by submitting and commenting on [pull requests](https://github.com/amitkgupta/kubernetes-release/pulls):
  * write or edit documentation
  * write unit, functional, or system tests
  * write code (**no patch is too small**: fix typos, add comments, clean up inconsistent whitespace)
  * refactor code

#### Submitting an Issue

When submitting a bug report, please include a [Gist](http://gist.github.com/) that includes a stack trace and any details that may be necessary to reproduce the bug, including your BOSH CLI version, BOSH Director version, BOSH stemcell version, etcd release version, and version of this release.

#### Submitting a Pull Request

1. Fork the project.
1. Create a topic branch.
1. Implement your feature or bug fix.
1. Commit and push your changes.
1. Submit a pull request.
