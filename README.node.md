# Prepare Node
This preparation (like all files here) assumes you're running Ubuntu 18.04.  This will get you in a state where you can perform installation

- Enable ssh
    - `sudo apt-get install ssh`
    - log in to all nodes to execute the following simultaneously
- Sudo No Password
	```bash
    sudo visudo
    ```
    Add the following line
	
    ```(Nano is opened) — add the following line, as the last line as the last line (to ensure it get's applied)
	      marcstreeter ALL=(ALL) NOPASSWD:ALL
    ```
- Set static ip for each node [more info](https://www.tecmint.com/configure-network-static-ip-address-in-ubuntu/)
    - Get gateway: `ip route | grep default` (remember 192.168.1.X gateway ip and enXXXX)
    - create/update file /etc/netplan/01-netcfg.yaml [view template](./templates/01-netcfg.yaml)
    -  Run `sudo netplan apply` to apply
- Setup ssh key connection
    - Install vim 
    -   sudo apt-get install vim
    - Make sure you clear out any entries in `~/.ssh/known_hosts` that match your node ip’s
    - ssh-copy-id <USERNAME>@<IP-ADDRESS-NOT-HOSTNAME-IN-HOSTS-FILE>