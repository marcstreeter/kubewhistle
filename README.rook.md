# Install Rook

Assuming you've prepared your cluster for rook as mentioned in the other README's

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
- Install Cluster (now that all previous deploys have finished)
```
kubectl appy -f cluster.yaml
```

- interesting CEPH links
    - https://github.com/rook/rook/blob/v1.0.0/Documentation/ceph-cluster-crd.md
    - https://github.com/rook/rook/blob/v1.0.0/Documentation/ceph-quickstart.md
    - https://forums.rancher.com/t/rancher-2-x-rook-io-problem/10503/8
    - https://rook.io/docs/rook/v1.0/flexvolume.html#platform-specific-flexvolume-path

# Create storage class and set as your default persistent storage
If you don't want to have to specify the storage class when you deploy projects. Set the default [from the, storage-class.yaml, manifest](./manifests/storage-class.yaml) which applies the required annotation
```
kubectl apply -f storage-class.yaml
```
Confirm it's set with 
```
kubectl get sc
```
*based on https://rook.io/docs/rook/v0.9/ceph-block.html (removed `fstype: xfs` line to default instead to ext4)*

# Enable the Ceph Dashboard Externally
Using the ingress controller
```
kubectl apply -f rook-dashboard-ingress.yaml
```
or the NodePort (which requires more work to add an entry in your HAProxy config)
```
kubectl apply -f rook-dashboard-nodeport.yaml
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