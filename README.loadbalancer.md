
LOADBALANCER (pass: k8s / cylance123)
- Using raspberry pi (pi/cylance123)
- Static IP
    - https://raspberrypi.stackexchange.com/questions/37920/how-do-i-set-up-networking-wifi-static-ip-address/74428#74428
    - /etc/dhcpcd.conf
- Sudoers
    - https://www.raspberrypi.org/documentation/linux/usage/users.md
- Enable ssh
    - https://www.raspberrypi.org/documentation/remote-access/ssh/
- HAProxy
    - https://serversforhackers.com/c/load-balancing-with-haproxy
    - http://gregtrowbridge.com/setting-up-a-multiple-raspberry-pi-web-server-part-5/
    - Remember listen stats (should have the port binding on the next line with a bind directive
    - This file `/etc/haproxy/haproxy.cfg`
        - Any changes require a restart
        - 
