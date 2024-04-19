# user-mount
Kubernetes PVC mounted in user home directory

This software allows a linux user on a single-node kubernetes cluster to mount PVC into home directory of that user.

The PVC is mounted when both sides allow it:

- kubernetes user allows it in the annotation of the PVC
- linux user allows it in the config file in his home directory

Currently, this project supports only PVC bound to hostPath PV. It is suggested to use hostPath provisioner https://github.com/rimusz/hostpath-provisioner.
