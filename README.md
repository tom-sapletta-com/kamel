# kamel
kamel kubectl camel-k


[Install kubectl on Fedora using the Snap Store | Snapcraft](https://snapcraft.io/install/kubectl/fedora)

> ## Enable snaps on Fedora and install kubectl
> 
> Snaps are applications packaged with all their dependencies to run on all popular Linux distributions from a single build. They update automatically and roll back gracefully.
> 
> Snaps are discoverable and installable from the [Snap Store](https://snapcraft.io/store), an app store with an audience of millions.
> 
> ![](https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_169,h_159/https://assets.ubuntu.com/v1/c93d842f-fedora.png)
> 
> ### Enable snapd
> 
> Snap can be installed on Fedora from the command line:
> 
>     sudo dnf install snapd
>     
> 
> Either log out and back in again, or restart your system, to ensure snap’s paths are updated correctly.
> 
> To enable _classic_ snap support, enter the following to create a symbolic link between `/var/lib/snapd/snap` and `/snap`:
> 
>     sudo ln -s /var/lib/snapd/snap /snap
>     
> 
> ### Install kubectl
> 
> To install kubectl, simply use the following command:
> 
>     sudo snap install kubectl --classic
