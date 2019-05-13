# Prepare Node
This preparation (like all files here) assumes you're running Ubuntu 18.04.  This will get you in a state where you can perform installation

# On each node individually
- Enable ssh
    - `sudo apt-get install ssh`
    - log in to all nodes to execute the following simultaneously

# Prepare All Nodes (masters and minions alike)
Connect to each node and simultaneously (using iTerm or tmux) do the following
- Sudo No Password
	```bash
    sudo visudo
    ```
    Add the following line as the last line (to ensure it get's applied)
    ```
    marcstreeter ALL=(ALL) NOPASSWD:ALL
    ```
 - Install vim
    ```bash
    sudo apt-get install vim
    ```
- Set static ip for each node [more info](https://www.tecmint.com/configure-network-static-ip-address-in-ubuntu/)
    - Get gateway: `ip route | grep default` (remember 192.168.1.X gateway ip and enXXXX)
    - create/update file `/etc/netplan/01-netcfg.yaml` [view template](./templates/01-netcfg.yaml)
    -  Run `sudo netplan apply` to apply

# Prepare your laptop to communicate with each node
- Setup ssh key connection
    ```
    ssh-copy-id <USERNAME>@<IP-ADDRESS-NOT-HOSTNAME-IN-HOSTS-FILE>
    ```
**Make sure you clear out any entries in `~/.ssh/known_hosts` that match your node ip’s**
