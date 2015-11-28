## kubernetes-release

This project is an experiment to see if open source kubernetes and supporting software from coreos (e.g. flannel) can be deployed and managed in a production-suitable manner, as opposed to [this](https://github.com/kubernetes/kubernetes/tree/master/cluster).

#### TODO:

- user [this](https://coreos.com/kubernetes/docs/latest/configure-kubectl.html) and [this](https://coreos.com/kubernetes/docs/latest/deploy-addons.html) to set up the DNS stuff
- add LICENSE, etc.
- add final blobstore
- why does master not start up right away?
- why everything complains about 10.244.5.2 (master IP)?
- is any of it resilient to restarts?
- see if everything can be run not as root
