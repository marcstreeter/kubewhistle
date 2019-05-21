# K3S: Kubernetes IoT (We're using RPi's)
This guide get's you going with a cluster of Raspberry Pi's using Raspian Stretch Lite.

# Initial Preparation
- [Prepare Raspberry Pi's](./README.rpi.md)
- [Make sure one is your load balancer](./README.loadbalancer.md)

# Installation of [K3S](https://k3s.io)([git](https://github.com/rancher/k3s))
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
- Add Raspberry Pi specific settings and restart
```bash
echo -n " cgroup_memory=1 cgroup_enable=memory" | sudo tee -a /boot/cmdline.txt
sudo shutdown -r now
```

# Server Creation
K3S is meant for single master (not HA). On your master 
```bash
sudo k3s server
```

