# Prepare: RaspberryPi
After installing fresh copy of Raspian Stretch/Buster Lite

# Update basic config
Via the Raspberry Pi config:
```bash
sudo raspi-config
```
Update the following options
- Change Default Password (1 Change User Password)
- Enable SSH (5 Interfacing Options/P2 SSH)
- Update Keyboard Layout (4 Localisation Options/I3 Change Keyboard Layout)
- Provide Unique FQDN* (2 Network Options/N1 Hostname)

*FQDN must be unique otherwise nodes [will not talk to eachother](https://github.com/rancher/k3s/issues/746)(ie k3s-worker-1, k3s-worker-2, etc)*

# Install Basic Sofware
```bash
sudo apt install vim
```
# Make IP Static (Based on [this answer](https://raspberrypi.stackexchange.com/a/74428))
Edit `/etc/dhcpcd.conf` with contents of [template](./templates/dhcpcd.conf) and then restart.

*find router ip with `ip route | grep default | awk '{print $3}'`*

# Given  < [v0.9](https://github.com/rancher/k3s/milestone/10) and Debian Buster [Change IPTables](https://github.com/rancher/k3s/issues/703)
Apply the following to prevent issues with Traefik or other networking
```
sudo iptables -F
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
```


## OTHER USEFUL LINKS

- [swap off](https://askubuntu.com/a/1231494)
- [swap off again?](https://serverfault.com/a/994813)
- [more swap off](https://www.raspberrypi.org/forums/viewtopic.php?t=244130#p1490692)
