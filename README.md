## kubernetes-release

This project is an experiment to see if open source kubernetes and supporting software from coreos (e.g. flannel) can be deployed and managed in a production-suitable manner, as opposed to [this](https://github.com/kubernetes/kubernetes/tree/master/cluster).

#### TODO:

- use [this](https://coreos.com/kubernetes/docs/latest/configure-kubectl.html) and [this](https://coreos.com/kubernetes/docs/latest/deploy-addons.html) to set up the DNS stuff as an errand
- add LICENSE and NOTICE
- why does master not start up right away?
- check all CTL logs for errors/warnings of things not actually working correctly
- is any of it resilient to recreates?
- can /var, especially /var/lib for state -- if it {needs,doesn't need} to persist, see if it can be forced into /var/vcap/{store,data}
- see if everything can be run not as root
