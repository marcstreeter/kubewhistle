# K3S: Kubernetes IoT (We're using RPi's)
This ~~plagiarized~~ [inspired](https://blog.alexellis.io/test-drive-k3s-on-raspberry-pi/) guide get's you going with a cluster of Raspberry Pi's using Raspian Stretch Lite, driven by your laptop.
```bash
┌workers─────┐                                                      
│            │                                ╔═══════════════╗     
│ RPi━━━━━━┓ │                                ║ > kubectl...  ║     
│ ┃WORKER 1┃◀┼────┐                           ║               ║     
│ ┗━━━━━━━━┛ │    │                           ║               ║     
│            │    │                     ┌─────║               ║     
│ RPi━━━━━━┓ │    │      RPi━━━━━━┓     │     ║               ║     
│ ┃WORKER 2┃◀┼────┼─────▶┃ MASTER ┃◀────┘     ║               ║     
│ ┗━━━━━━━━┛ │    │      ┗━━━━━━━━┛           ║               ║     
│            │    │                           ╚═══════════════╝     
│    ...     │    │                          /.-.-.-.-.-.-.-.-.\    
│            │    │                         /.-.-.-.-.-.-.-.-.-.\   
│ RPi━━━━━━┓ │    │                        /.-.-.-.-.-.-.-.-.-.-.\  
│ ┃WORKER N┃◀┼────┘                       /______/__________\___o_\ 
│ ┗━━━━━━━━┛ │                            \_______________________/ 
└────────────┘                                                      
```

# Initial Preparation
After initial preparation
- [Prepare Raspberry Pi's](./README.rpi.md)
- [Make sure one is your load balancer](./README.loadbalancer.md)

Also add k3s specific settings and restart(note: `boot/cmdline.txt` may be [`boot/firmware/cmdline.txt`](https://github.com/rancher/k3s/issues/1078))
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

### Master Initialization
```bash
curl -sfL https://get.k3s.io | sh -
```
After installation finishes, copy the token for worker installation
```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```
*NOTE: this just shows the token, you'll need to copy it over in the next steps*

### Worker(s) Installation
On each Raspberry Pi device in your cluster do the following, substituting your details:
```bash
curl -sfL https://get.k3s.io | K3S_TOKEN=<TOKEN> K3S_URL=https://<SERVER-IP>:6443 sh -
```
*NOTE: substitute your unique token and ip*

# Laptop Preparation
Most likely you'll want to drive the RPi cluster from a laptop. From your laptop run

```bash
mkdir ~/.kube
scp pi@192.168.1.141:/etc/rancher/k3s/k3s.yaml ~/.kube/config
vi ~/.kube/config  # replace 'localhost' with k3s server ip (i.e 192.168.1.141)
```

## Other Art
Started noticing another places with a similar idea
- https://github.com/bbruun/k3s-getting-started
- https://github.com/rancher/k3s/issues/795
