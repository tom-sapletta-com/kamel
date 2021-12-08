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

    sudo snap install kubectl --classic\
    
    
    
[Install the kubernetes as single node in Fedora 28 | Hwchiu Learning Note](https://www.hwchiu.com/kubernetes-fedora.html)

Remember, you need to remove all installed docker before you install the docker-ce and you can use the following command to remove the existing docker packages.  

sudo dnf remove docker \\  
 docker-client \\  
 docker-client-latest \\  
 docker-common \\  
 docker-latest \\  
 docker-latest-logrotate \\  
 docker-logrotate \\  
 docker-selinux \\  
 docker-engine-selinux \\  
 docker-engine  

First, we need to add additional repo about docker-ce into the dnf system.  

sudo dnf config-manager \\  
 --add-repo \\  
 https://download.docker.com/linux/fedora/docker-ce.repo  

Then, we can install the docker-ce and activate it by systemd system.  

sudo dnf -y install docker-ce  
sudo docker version  
sudo systemctl enable docker  
sudo systemctl start docker  

In addition to docker, there’re some issues that we need to solve for the kubernetes.

1.  firewalld, we must sure the default firewall won’t block the port of 6443/10250.
2.  it’s still not support to run kubeadm with the swap. we need to disalbe it.

sudo systemctl stop firewalld  
sudo systemctl disable firewalld  
sudo swapoff -a && sudo sysctl -w vm.swappiness=0  

# [](https://www.hwchiu.com/kubernetes-fedora.html#Tools "Tools")Tools

After installing the docker-ce as the container environment for kubernetes, the next parts is the tools of kubernetes.

Just like the `docker-ce`, we can’t find those tools in the default repo, hence, we need to add additional repo to dnf system now.

Before we use the `kubeadm` to install the kubernetes, we have to install three packages we need. the `kubectl`, `kubelet` and `kubeadm`.

Since there’re not any existing repo file about kubernetes for fnd/yum system, we need to create it by ourself.

cat <<EOF /etc/yum.repos.d/kubernetes.repo  
\[kubernetes\]  
name=Kubernetes  
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86\_64  
enabled=1  
gpgcheck=1  
repo\_gpgcheck=1  
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg  
exclude=kube\*  
EOF  

If you want to install the specific version of kubernetes tools, use the `sudo dnf list --showduplicates kube\*` to see all verions. Otherwise, use the following comand to install the latest verion of kubernetes.

We need to use the `--disableexcludes` to disable the exclude list of kubernetes, since all tools are in that kubernetes repo.  
After installing that, we also need to enable the systemd service for kubelet.

sudo dnf install -f kubelet kubectl kubeadm --disableexcludes=kubernetes  
sudo kubectl version  
sudo kubeadm version  
sudo kubelet --version  
sudo systemctl enable kubelet  

# [](https://www.hwchiu.com/kubernetes-fedora.html#kubernetes "kubernetes")kubernetes

Now, I will ues the `kubeadm` to install the kubernetes environment now.  
The important thing is that you need to take care the parameter of your `kubeadm` process, if you choose the `flannel` as your CNI(Containre Network Interface). You use pass the `--pod-network-cidr=10.244.0.0/16` and the `flannel` can use that to choose the IP address range of each node.

This init procedure takes time to install whole kubernetes, the most part of that is used to download the dokcer image for all kubernetes components.



[Install the kubernetes as single node in Fedora 28 | Hwchiu Learning Note](https://www.hwchiu.com/kubernetes-fedora.html)

Now, follow the instructions to setup the kubeconfig for your regular user.

mkdir -p $HOME/.kube  
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
sudo chown $(id -u):$(id -g) $HOME/.kube/config  

With that kubeconfig, we can use the `kubectl` to control the kubernetes now and you can use the `kubectl get nodes` to see the status of the node.

Since we have not installed the CNI in the cluster, the status is `NotReday` and it will become the `Ready` once you install any CNI into it.  

hwchiu➜~» kubectl get nodes                                                                                                                        \[23:50:27\]  
NAME                    STATUS     ROLES     AGE       VERSION  
localhost.localdomain   NotReady   master    3m        v1.11.3  
  
hwchiu➜~» kubectl apply -f  https://raw.githubusercontent.com/coreos/flannel/v0.9.1/Documentation/kube-flannel.yml                                 \[23:50:39\]  
clusterrole.rbac.authorization.k8s.io/flannel created  
clusterrolebinding.rbac.authorization.k8s.io/flannel created  
serviceaccount/flannel created  
configmap/kube-flannel-cfg created  
daemonset.extensions/kube-flannel-ds created  
  
hwchiu➜~» kubectl get nodes                                                                                                                        \[23:51:30\]  
NAME                    STATUS    ROLES     AGE       VERSION  
localhost.localdomain   Ready     master    5m        v1.11.3  

# [](https://www.hwchiu.com/kubernetes-fedora.html#Deploy-Pod "Deploy Pod")Deploy Pod

Unfortunately, we still can’t run a Pod successfully, the reason is that the `master` node doesn’t be allowed to deploy any Pods if we use the `kubeadm` to setup the kubernetes cluster.  
In my condition, I have only one node and it must be master node, that’s why any user-defined Pod will be pending forever.

The mechanism about that limitation is the `taint` and we can use the following command to remove the taint rules of all master node.  

kubectl taint nodes --all node-role.kubernetes.io/master-  

Now, we can deploy a Pod and enjoy your kubernetes cluster.  

kubectl run test --image=hwchiu/netutils  
kubectl get pods -o wide -w  

# [](https://www.hwchiu.com/kubernetes-fedora.html#Helm-Chart-Optional "Helm Chart (Optional)")Helm Chart (Optional)

There’s a package system like the `deb` or something else in the kubernetes ecosystem, and it’s called `Helm Chart`.  
You can use that to install some predefined packages which contains all resources you need, such as the deployment,service,configmap,RBAC and others into your kubernetes cluster.

Again, it’s optional tool and you can install it if you need it.

You can follow the [official document](https://docs.helm.sh/using_helm/) to install the `helm chart` but you will meet some problems.

I will note those problems and share how I solve those problems.

## [](https://www.hwchiu.com/kubernetes-fedora.html#Install "Install")Install

Since we are use the `Fedora` now, the `helm` has not been takend by any dnf repos, we need to download the binary from the official website and you can use the following script to download the binary automatically.  

$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get get\_helm.sh  
$ chmod 700 get\_helm.sh  
$ ./get\_helm.sh  

You will be informed to use the `helm init` to initial the helm environment in your kubernetes cluster after executing the `get_helm.sh`, **but don’t do that now.**

Since we use the `kuberadm` to install the kubernetes and it use the `RBAC` mode as default. We need to add another parameter for the `RBAC` mode.

You need to prepare the RBAC config for your `helm chart` service before you using it to install any packages.  
And you can find the [whole tutoral here](https://docs.helm.sh/using_helm/#role-based-access-control)

After create the `RBAC` config, use the follwing command to init your `helm`  

    helm init --service-account tiller  


# [](https://www.hwchiu.com/kubernetes-fedora.html#Test "Test")Test

Now, using the following command to install the nginx ingress package by `helm`.

    helm install --name my-release stable/nginx-ingress  

Use the `kubectl` to see all the kubernetes resource we installed by the helm  


    kubectl get all | grep my-release
    
    
    
