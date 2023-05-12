# Clean up the environment
<p align="left">
<img src="/docs/images/cleaning.png" width="70">
</p>

Unsolved issue or just want to start over, this guide is for you!

## Keep the virtual machine, just clean up the Kubernetes cluster

#### Remove the node

Run this to gracefully evict all the running pods on the node:

    kubectl drain <node name> --delete-emptydir-data --force --ignore-daemonsets

Reset the state installed by kubeadm

    kubeadm reset

The reset process does not reset or clean up iptables rules or IPVS tables. If you wish to reset iptables, you must do so manually:

    iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X

If you want to reset the IPVS tables, you must run the following command:

    ipvsadm -C

Now remove the node:

    kubectl delete node <node name>

If you wish to start over, run `kubeadm init` or `kubeadm join` with the appropriate arguments.

#### Clean up the control plane

Performs a best-effort revert of changes made to this host by `kubeadm init`

    sudo kubeadm reset --kubeconfig="/etc/kubernetes/admin.conf"

`--kubeconfig=string`: Delete the kubeconfig file to use when talking to the cluster. If the flag is not set, a set of standard locations can be searched for an existing kubeconfig file. (If you config to `non-root user` after `kubeadm init`, don't forget to also delete `$HOME/.kube/config`)

The reset process does not reset or clean up iptables rules or IPVS tables. If you wish to reset iptables, you must do so manually as we do in [Remove the node](#remove-the-node).

## Dispose of all virtual machines

Since we use `Vagrant` to automate provisioning the virtual machines, you can dispose of all that with just a command. Run this in the project directory of your host, which installs `Vagrant` & `Virtual Box`:

    vagrant destroy

>If you just want to turn off the virtual machines, run `vagrant halt` instead. When you `vagrant up` again, all your work will not be remove. Read more about this command [here](https://developer.hashicorp.com/vagrant/docs/cli/halt).