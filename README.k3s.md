# K3S: Kubernetes IoT (We're using RPi's)
This ~~plagiarized~~ [inspired](https://blog.alexellis.io/test-drive-k3s-on-raspberry-pi/) guide get's you going with a cluster of Raspberry Pi's using Raspian Stretch Lite.

# Initial Preparation
After initial preparation
- [Prepare Raspberry Pi's](./README.rpi.md)
- [Make sure one is your load balancer](./README.loadbalancer.md)

Also add k3s specific settings and restart
```bash
cp /boot/cmdline.txt ~/cmdline.txt # copy the original in case there are mistakes
sudo python -c "
options = 'cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory'
path = '/boot/cmdline.txt'
value = open(path,'r').read()
open(path,'w').write('{0} {1}\n'.format(value.strip(), options))
"
sudo shutdown -r now
```

# Installation of [K3S](https://k3s.io)([git](https://github.com/rancher/k3s))


## General Installation
Download and install latest release of k3s (which establishes systemd unit aswell)
```bash
curl -sfL https://get.k3s.io | sh -
```
### Master Initialization
```bash
sudo k3s server
```

### Worker(s) Installation
On each Raspberry Pi device in your cluster do the following:
- Download K3S v0.5 ([or latest](https://github.com/rancher/k3s/releases))
```bash
wget -O k3s https://github.com/rancher/k3s/releases/download/v0.5.0/k3s-armhf && \
chmod +x k3s && \
sudo mv k3s /usr/local/bin/k3s
```
- Install systemd service
```bash
wget https://raw.githubusercontent.com/rancher/k3s/master/k3s.service && \
sudo chown root:root k3s.service && \
sudo chmod 777 k3s.service && \
sudo mv k3s.service /etc/systemd/system/k3s.service
```


# Server Creation
K3S is meant for single master (not HA). On your master 
```bash
sudo k3s server
```

