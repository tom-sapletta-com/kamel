# kamel
kamel kubectl camel-k


[apache/camel-k: Apache Camel K is a lightweight integration platform, born on Kubernetes, with serverless superpowers](https://github.com/apache/camel-k)


    kubectl config view --flatten

    
    kamel run --name=withrest routes-rest.js



+ [Install and Set Up kubectl on Linux | Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)



## [Install kubectl on Fedora using the Snap Store | Snapcraft](https://snapcraft.io/install/kubectl/fedora)

## Enable snaps on Fedora and install kubectl

Snaps are applications packaged with all their dependencies to run on all popular Linux distributions from a single build. They update automatically and roll back gracefully.

Snaps are discoverable and installable from the [Snap Store](https://snapcraft.io/store), an app store with an audience of millions.

### Enable snapd

Snap can be installed on Fedora from the command line:

    sudo dnf install snapd
    

Either log out and back in again, or restart your system, to ensure snap’s paths are updated correctly.

To enable _classic_ snap support, enter the following to create a symbolic link between `/var/lib/snapd/snap` and `/snap`:

    sudo ln -s /var/lib/snapd/snap /snap
    

### Install kubectl

To install kubectl, simply use the following command:

    sudo snap install kubectl --classic
