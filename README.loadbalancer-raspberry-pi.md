# Loadbalancer: RaspberryPi
After installing fresh copy of Raspian Stretch Lite

# Update basic config
Via the Raspberry Pi config:
```bash
sudo raspi-config
```
Update the following options
- Change User Password
- Interfacing Options (P2 SSH)
- Internationalization (Keyboard)
- Update 

# Install Basic Sofware
```bash
sudo apt install vim
```
# Make IP Static (Based on [this answer](https://raspberrypi.stackexchange.com/a/74428))
Edit `/etc/dhcpcd.conf` with contents of [template](./templates/dhcpcd.conf) and then restart.

*find router ip with `ip route | grep default | awk '{print $3}'`*

# Install HAProxy
After installing HAProxy
```bash
sudo apt install haproxy
```
[Follow HAProxy the Guide](./README.haproxy.md) to enable it as a loadbalancer.
