# Kubespray
Here we take the kubespray route

*currently broke waiting for input on following issues*
- https://github.com/kubernetes-sigs/kubespray/pull/4783 [MERGED]
- https://github.com/kubernetes-sigs/kubespray/issues/4784 [MERGING]

# Ansible Prep
- Ansible (Node) Preparation
    - Install python
        - `sudo apt-get install python-minimal`
    - Install pip
        - May only need to do `sudo apt install python-pip`
        - If that fails, Refer [to locating pip](https://askubuntu.com/questions/1061486/unable-to-locate-package-python-pip-when-trying-to-install-from-fresh-18-04-in)
- Ansible (Client) Preparation
    - Creat python virtual environment, activate
      ```bash
      conda create -n kubespray python=3
      source activate kubespray
      ```
    - Clone and Checkout a specific [version](https://github.com/kubernetes-sigs/kubespray/tags) of Kubespray
    ```bash
    git clone https://github.com/kubernetes-sigs/kubespray.git
    cd ./kubespray
    git checkout tags/v2.10.0
    pip install -r requirements.txt
    ```
    - Establish Nodes/Masters
        - Update Ansible Hosts file [from tempate](./templates/inventory.ini)
        ```bash
        cp -rfp inventory/sample inventory/XXXX
        vi inventory/XXXX/inventory.ini
        ```
    - Enable Helm
        - Make sure helm enabled in `inventory/XXXX/group_vars/k8s-cluster/addons.yml`
    - Enable Remote Control 
        - Make change in `inventory/XXXX/group_vars/k8s-cluster/k8s-cluster.yml`
        - Set `supplementary_addresses_in_ssl_keys` with external ip (should see a comment there about how to)
        - NOTE: if you’re adding it after the fact, you’ll have to delete the `/etc/kubernetes/ssl/apiserver.*` cert files on each master (that way running it again replaces those certs
            - Check https://github.com/kubernetes-sigs/kubespray/issues/2164
            - Also example https://github.com/kubernetes-sigs/kubespray/issues/1430
    - PRE-CHECK node/master connectivity
        - Check to make sure that all are accessible
        ```bash
        ansible -i inventory/<YOUR_DIRECTORY>/inventory.ini all -m ping
        ```
    - RUN INSTALL 
    ```bash
    ansible-playbook -b -v -i inventory/<YOUR_DIRECTORY>/inventory.ini -u marcstreeter cluster.yml 
    ```
    
    *instead of using `-u marcstreeter` you could instead up the `ansible.cfg` with [remote user](./templates/ansible.cfg)*
￼
# Post Install
- Enable Kubectl
    - on the computer (client laptop or master node) you’ll be using to control your cluster, set your context
        - NOTE IF YOU DON’T DO THIS THAN YOU WILL SEE THIS ERROR: “The connection to the server localhost:8080 was refused - did you specify the right host or port?”
    - Look at ‘kubectl config’ for steps on setting up config (which basically is the following commands)
        ```bash
        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config
        ```
    - It’s important that the version of `kubectl` you’ve got installed is the same as the server that you’re running
- Enable Dashboard ( https://github.com/kubernetes/dashboard/wiki/Creating-sample-user ) 
    - Create Service Account and Role Binding: `kubectl apply -f ./manifests/dashboard-admin.yaml`
    - Get token: `kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep dash-user | awk '{print $1}')`
    - Log in to Dash board
        - NOTE: if you can do regular kubectl calls you can do this
            May need to copy config from master to your laptop like so:
                ```bash
                rm -rf /Users/marcstreeter/.kube && \
                mkdir ~/.kube &&  \
                scp marcstreeter@192.168.1.211:/home/marcstreeter/.kube/config /Users/marcstreeter/.kube/config
                ```
            Also must have enabled remote connections from above
        - Run `kubectl proxy` (and open access to the dashboard) you:
        - Use [generated URL](http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login)
            - Copy the token from the previous step 
            - `kubectl cluster-info` (shows the link for it)
￼
# Other links
- https://schoolofdevops.github.io/ultimate-kubernetes-bootcamp/cluster_setup_kubespray/

Would like the sixth server but cannot access it as it’s on wifi.  Would be nice to have wifi devices be able to connect to wired devices
- Need to get a setup like so:   modem ——> router ——>——>wifi router (in AP, access point, mode) and switch.  That way devices are getting ip’s from router (who get’s 1 ip straight from 
    - Consult: https://www.asus.com/us/support/FAQ/1015009/
    - Consult: http://www.tomshardware.com/answers/id-3776081/wifi-router-ethernet-devices-eachother.html
    - 

