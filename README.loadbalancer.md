# LoadBalancer
 
After setting up your Raspberry Pi (RPi)

# Install HAProxy
After installing HAProxy
```bash
sudo apt install haproxy
```
[Follow HAProxy the Guide](./README.haproxy.md) to enable it as a loadbalancer.


- Sudoers
    - https://www.raspberrypi.org/documentation/linux/usage/users.md
- HAProxy
    - https://serversforhackers.com/c/load-balancing-with-haproxy
    - http://gregtrowbridge.com/setting-up-a-multiple-raspberry-pi-web-server-part-5/
    - Remember listen stats (should have the port binding on the next line with a bind directive
    - This file `/etc/haproxy/haproxy.cfg`
