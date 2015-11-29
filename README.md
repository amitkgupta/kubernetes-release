## kubernetes-release

This project is an experiment to see if open source [Kubernetes](http://kubernetes.io/) and supporting software from CoreOS (e.g. flannel) can be deployed and managed in a [production-suitable manner](http://bosh.io/), as opposed to [this](https://github.com/kubernetes/kubernetes/tree/master/cluster).

It follows the [Manual Installation Guide](https://coreos.com/kubernetes/docs/latest/getting-started.html) for deploying Kubernetes on CoreOS.

#### TODO:

- use [this](https://coreos.com/kubernetes/docs/latest/configure-kubectl.html) and [this](https://coreos.com/kubernetes/docs/latest/deploy-addons.html) to set up the DNS stuff as an errand
- why does master not start up right away?
- check all CTL logs for errors/warnings of things not actually working correctly
- is any of it resilient to recreates?
- scan /var, especially /var/lib for state -- if it {needs,doesn't need} to persist, see if it can be forced into /var/vcap/{store,data}
- see if everything can be run not as root

#### Known Issues

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

It appears that if you provide a CIDR range for the `pod_network` that's smaller than /23 (or maybe even smaller than /22), `flannel` will compute the wrong range.

See [this pull request](https://github.com/coreos/flannel/pull/378).

**Docker puts stuff in `/var/run/docker/netns` and `/var/lib/docker/network`**:

There doesn't seem to be a way to tell the `daemon` command to use different directories, like you can for `--graph` and `--execroot`.

See [`daemon` command documentation](https://docs.docker.com/engine/reference/commandline/daemon/).
