# Current Issues
These may block your desire to install rook

- https://github.com/rook/rook/issues/3157
- 


# Install Rook

Assuming you've prepared your cluster for rook as mentioned in the other README's.

## Prepare Rook
Currently there is [additional software](https://github.com/rook/rook/issues/2591) that is needed for rook to [prevent osd problems](https://github.com/bigbitbus/rook/commit/5668131853bf57f20b86508049bd713b44befe0d). LVM2 must be installed
```bash
sudo apt-get install -y lvm2
sudo shutdown -r now
```
**maybe in some future version of rook, this won't be needed, as of v1.0.0 it is needed**

## Now you may install
- Checkout the appropriate version:
```
git clone https://github.com/rook/rook.git
cd rook
git checkout tags/v1.0.0
cd cluster/examples/kubernetes/ceph
```
- Install slowly checking to make sure everything finishes before moving onto the next step
```
kubectl apply -f common.yaml
kubectl apply -f operator.yaml
kubectl -n rook-ceph get pod --watch
```
*after about ~1 minute you should see a couple rook-ceph-agents, rook-ceph-operator, and a couple rook-discovers in running state*
- Install Cluster (now that all previous deploys have finished)
```
kubectl appy -f cluster.yaml
```

- interesting CEPH links
    - https://github.com/rook/rook/blob/v1.0.0/Documentation/ceph-cluster-crd.md
    - https://github.com/rook/rook/blob/v1.0.0/Documentation/ceph-quickstart.md
    - https://forums.rancher.com/t/rancher-2-x-rook-io-problem/10503/8
    - https://rook.io/docs/rook/v1.0/flexvolume.html#platform-specific-flexvolume-path

# Create default storage class
If you want a default storage class set it using  the, [storage-class.yaml](./manifests/storage-class.yaml), manifest which applies the required annotation
```
kubectl apply -f ./manifests/storage-class.yaml
```
Confirm it's set with 
```
kubectl get sc
```
*based on https://rook.io/docs/rook/v1.0/ceph-block.html (removed `fstype: xfs` line to default instead to ext4)*

# Enable External Ceph Dashboard
*dashboard [isn't working](https://github.com/rook/rook/issues/3106) with v1.0.0*
Using the ingress controller 

```
kubectl apply -f ./manifests/rook-dashboard-ingress.yaml
```
Get your password to login in with `admin`:
```
kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo
```

# Testing
- From here on should be able to create PV with PVC like in:
    - rook/cluster/examples/kubernetes/mysql.yaml
    - Keep in mind that you’ll generally want to destroy PVC before you destroy PV

# Interesting links
- https://www.linkedin.com/pulse/rookio-ceph-persistent-storage-made-easy-kubernetes-gokul-chandra
- 
