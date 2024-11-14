# user-mount
Kubernetes PVC mounted in user home directory

This software allows a linux user on a single-node kubernetes cluster to mount PVC into home directory of that user.

The PVC is mounted when both sides allow it:

- kubernetes user allows it in the annotation of the PVC
- linux user allows it in the config file in his home directory

Currently, this project supports only PVC bound to hostPath PV. It is suggested to use hostPath provisioner https://github.com/rimusz/hostpath-provisioner.

## How to install

1. clone this repository into a local directory
    ```bash
    user$ cd /tmp; git clone https://github.com/milan-simanek/user-mount
    user$
2. run install script as root
    ```bash
    root# cd /tmp/user-mount; ./install
    root#

## How to use it
Let say `user1` runs in kubernetes some workload which uses persistent volume claim (PVC) of name `vol1` in kubernetes namespace `user1ns`. The user can still run `kubectl exec` and modify the content of the volume as root, but it is not so comfortable.

This software can help the user by mounting the volume into the user home directory. Let `user-mount` software is installed and running.
In order to mount a PVC, the user has to:
1. Signal the PVC it can be mounted by the user by setting `user-mount` annotation equal to the list of local linux users permitted to mount it separated by spaces:
   ```yaml
   kind: PersistentVolumeClaim
   metadata:
     name: vol1
     annotations:
       user-mount: user1
   ...
2. As linux user `user1` request mounting the volume by specifying the volume in `$HOME/volume.conf`. Each line specifies one volume to be mounted in format `namespace/pvcName`:
   ```bash
   $ cat $HOME/.volume.conf
   user1ns/vol1
3. The volume is automatically mounted into mountpoint `$HOME/.volume/namespace/pvcname` and all the files owned by `root` are visible as owned by `user1`, so that `user1` can easily manage the content:
   ```bash
   $ ls -l $HOME/.volume/user1ns/
   drwxr-xr-x. 5 user1 0 4096 Nov 14 17:27 vol1
   $
    
