# Portworx (does not support k3s at this moment)
So you want to try portworx storage eh?

### Pre-requisites
You will need 
- 3 worker nodes (that's at least 4 computers total)
- 6 disks (3 for your kubernetes installation and 3 for portworx storage)

### Node preparation
These are the steps to take to prepare computers for portworx installation
#### Ports
Open ports on each computer between eachother, heres a [template](./templates/ufw_ports.md)

```
    sudo ufw allow from 192.168.1.<IP> to any port 9001:9022 proto tcp
    sudo ufw allow from 192.168.1.<IP> to any port 9002 proto udp
    sudo ufw allow from 192.168.1.<IP> to any port 32678:32679 proto tcp
    sudo ufw allow from 192.168.1.<IP> to any port 2379 proto tcp
```

#### Disable Swap
Ensure swap is off permanently

```
swapoff -a
vi /etc/fstab  # comment out lines with word "swap" using hashmark
sudo shutdown -r now
```

#### Prepare disks
You must first make sure that the drive has been formatted and has no partitions on it at all (including MBR data), I formatted the drive using
```
dd if=/dev/zero of=/dev/sdX bs=512 count=1 conv=notrunc
```
*replace `sdX` with something concrete, you can do `lsblk` to see your devices*
I didn't even wait for it to finish...I just let it write zero's for about a minute and then cancelled it and did a normal format (from the GUI)
such that the device no longer showed it had MBR data
*one tell tale sign is that when you finally do the formatting it will not have sdX1 or sdX2, it will only have sdX*

You then make a symlink to the drive named the same thing throughout your different computers (you will most likely find
that the target disks have different names within `dev` (which will cause complications for the portworx installer), make
the following in `/etc/udev/rules.d/portworx.rules`:
```
KERNEL=="sdX", SYMLINK+="portworx"
```
*substituing `sdX` for something concrete like `sdc` or `sdb` (depending on what drive you are giving to portworx)*

Reboot, to give it a chance to create your symlinks
```
sudo shutdown -r now
```
#### Prepare nodes
Because we are using the builtin option for the etcd component of portworx, we need to label nodes with `px/metadata-node=true` like so([see template](./templates/portworx-node-labels.yaml)):
```bash
kubectl label nodes <your-node-name> px/metadata-node=true
```
### Install Portworx

Generate the kubectl manifest by providing these options to the [portwork manifest generator](https://install.portworx.com)
```
Kubernetes Version: 1.13.5
ETCD: Built-In #label nodes for builtin kvdb using given command
Select your environment: OnPrem
Select type of OnPrem storage: Manually specify disks 
Drive/Device: /dev/portworx  
Skip KVDB metadata device: checked

Data Network Interface: auto
Management Network Interface: auto

Are you running either of these? None
```
Now apply the generated manifest with the provided link (or by running [this template](./templates/portworx.yaml) locally) like so:
```
kubectl apply -f 'https://install.portworx.com/2.0?mc=false&kbver=1.13.5&b=true&s=%2Fdev%2Fportworx&c=px-cluster-0a5a3ba8-c5b0-40f6-820a-15488019f76f&stork=true&lh=true&st=k8s'
```

### Validate install

Check to make sure that all portworx pods are finished loading (can take ~5 minutes)
```
kubectl get po -n kube-system --watch
```

Then run the following on all of you portworx nodes:
```
/usr/local/bin/pxctl status
```
should output all drives from all computers

### Test install

You can see the lighthouse UI

```
kubectl port-forward <PODNAME>  31684:80 -n kube-system
```
*Then connect to the UI locally http://127.0.0.1:31684/login*
