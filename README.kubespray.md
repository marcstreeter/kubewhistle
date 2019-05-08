# Kubespray
Here we take the kubespray route

# Ansible Prep
- Ansible (Node) Preparation
    - Install python
        - sudo apt-get install python-minimal
    - Install pip
        - May only need to do `sudo apt install python-pip`
        - If that fails, Refer [to locating pip](https://askubuntu.com/questions/1061486/unable-to-locate-package-python-pip-when-trying-to-install-from-fresh-18-04-in)
- Ansible (Client) Preparation
    - Creat python virtual environment, activate
        - `conda creat -n kubespray python=3`
        - `source activate kubespray`
    - Clone and Checkout a specific [version](https://github.com/kubernetes-sigs/kubespray/tags) of Kubespray
        - `git clone https://github.com/kubernetes-sigs/kubespray.git`
        - `cd ./kubespray`
        - `git checkout tags/v2.9.0`
        - `pip install -r requirements.txt`
    - Establish Nodes/Masters
        - Update Ansible Hosts file [from tempate](./templates/inventory.ini)
        - `cp -R sample XXXX`
        - `vi inventory/XXXX/inventory.ini`
    - Enable Helm
        - Update Ansible Config File [from template](./templates/ansible.cfg)
        - Makesure that `ansible.cfg` is specifying right user ( 
        - Make sure helm enabled in `inventory/XXXX/group_vars/k8s-cluster/addons.yml`
    - Enable Remote Control 
        - Make change in `inventory/prod/group_vars/k8s-cluster/k8s-cluster.yml`
        - Set `supplementary_addresses_in_ssl_keys` with external ip (should see a comment there about how to)
        - NOTE: if you’re adding it after the fact, you’ll have to delete the `/etc/kubernetes/ssl/apiserver.*` cert files on each master (that way running it again replaces those certs
            - Check https://github.com/kubernetes-sigs/kubespray/issues/2164
            - Also example https://github.com/kubernetes-sigs/kubespray/issues/1430
    - PRE-CHECK node/master connectivity
        - Check to make sure that all are accessible
        `ansible -i inventory/<YOUR_DIRECTORY>/inventory.ini all -m ping`
    - RUN INSTALL `ansible-playbook -b -v -i inventory/<YOUR_DIRECTORY>/inventory.ini cluster.yml`
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
    - Create Service Account and Role Binding: `kubectl apply -f ./manifests/dashboard-user.yaml`
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
            - kubectl cluster-info (shows the link for it)
- Enable Helm
    - (On master) helm init 
    - (On master) helm repo update
    - (On your local computer) `brew install kubernetes-helm` and `helm init —client-only`
- Enable Istio with Helm(Checked out 1.1.2)
    - Step 1 do init scripts
    ```bash
    helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
    ```
    - Step 2 install with bells and whistles on
    ```bash
    helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
    --set grafana.enabled=true \
    --set kiali.enabled=true \
    --set tracing.enabled=true \
    --set gateways.istio-ingressgateway.type=NodePort \
    --set gateways.istio-egressgateway.type=NodePort
    ```
- Enable Rook
    - https://rook.io/docs/rook/v0.9/ceph-quickstart.html
    - Under the “If you’re feeling lucky TL;DR”
    - Clone project and checkout latest tag
        - git clone https://github.com/rook/rook.git
        - git checkout tags/v0.9.2
    - Do the feeling lucky steps
        - cd rook/cluster/examples/kubernetes/ceph/
        - kubectl create -f operator.yaml
        - kubectl create -f cluster.yaml
    - Watch until it’s done
        - kubectl get pods --all-namespaces --watch
        - 
- https://rook.io/docs/rook/v0.9/ceph-block.html
    - Copy and created `storageclass.yaml` 
        - Make change, remove `fstype: xfs` line (will default to ext4)
        - Make annotation to enable it as the default storage class so you don’t have to specify storage classes over and over
- Enable Dashboard
    - Create a service to access it externally (as a NodePort)
    - 
- From here on should be able to create PV with PVC like in:
    - rook/cluster/examples/kubernetes/mysql.yaml
    - Keep in mind that you’ll generally want to destroy PVC before you destroy PV
- Next Steps
    - Need to get the dashboard working
        - https://www.linkedin.com/pulse/rookio-ceph-persistent-storage-made-easy-kubernetes-gokul-chandra
        - Probably can just kubectl apply one of the scripts in the examples
        - 

Spinnaker Install
- Depends on Rook install
- Updated PV/PVC on https://spinnaker.io/downloads/kubernetes/quick-install.yml
- https://medium.com/oracledevs/install-spinnaker-with-halyard-on-kubernetes-88277bd61d59
    - First install https://www.spinnaker.io/setup/install/halyard/
    - Then start doing it’s steps
    - Make sure only using one kubectl config https://github.com/spinnaker/spinnaker/issues/3278
- Updated HAproxy to point to the ports exposed in the steps that have these two lines
    - Make sure you follow the steps to convert the two services into NodePort (vs ClusterIP)
    - hal config security ui edit --override-base-url "http://demospinnaker:30900"
    - hal config security api edit --override-base-url "http://demospinnaker:30884"
    - Notice how I use “demospinnaker” and not an IP. The web UI will be exposing the URL and /etc/hosts can forward it on to the proper IP
- Next Steps
    - https://www.spinnaker.io/guides/tutorials/
    - https://www.spinnaker.io/guides/user/get-started/#users-deploying-with-spinnaker
    - https://www.spinnaker.io/guides/user/get-started/#using-spinnaker-the-high-level-process
    - https://thenewstack.io/getting-started-spinnaker-kubernetes/
    - https://www.spinnaker.io/guides/tutorials/codelabs/hello-deployment/
    - https://www.youtube.com/watch?v=To8iXsFMD9U
    - 

Jenkins X OnPrem (NOT YET READY as onprem not fully baked)
- Note: https://github.com/jenkins-x/jx/issues/1434 
- Makes Jenkins X work
- Install jx 
    - brew tap jenkins-x/jx
    - brew intall jx
- Using helm for ingress controller:
- helm install --name nginx-ingresser stable/nginx-ingress --set controller.service.type=NodePort --set controller.service.externalIPs={192.168.1.201,192.168.1.202,192.168.1.203} --set controller.service.externalTrafficPolicy=Local
- I don't think it's the below
	helm install --name nginx-ingresser stable/nginx-ingress \
          --set controller.service.type=NodePort \
          --set controller.service.externalIPs[0]=74.192.136.153 \
          --set controller.service.externalTrafficPolicy=Local


ISTIO NEXT STEPS
- Deploy sample app
    - Label namespace for automatic sidecar injection
kubectl label namespace default istio-injection=enabled

DASHBOARD
- https://k8s:6443/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login
- 
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
    - 
- Old Raspberry K8s setup
    - https://github.com/alexellis/k8s-on-raspbian/blob/master/GUIDE.md

NEXT STEPS
- Jenkins X
    - https://www.youtube.com/watch?v=iytHDaLb3-Q
    - https://jenkins.io/projects/jenkins-x/
    - 
- https://preliminary.istio.io/docs/tasks/telemetry/kiali/
    - Understand the different templates that are being applied, I don’t understand that helm command much
        - It looks like you’re supposed to funnel it into a make shift helm chart and then just apply it like normal
￼
FULL KUBESPRAY SETUP can be seen here: https://schoolofdevops.github.io/ultimate-kubernetes-bootcamp/cluster_setup_kubespray/

Would like the sixth server but cannot access it as it’s on wifi.  Would be nice to have wifi devices be able to connect to wired devices
- Need to get a setup like so:   modem ——> router ——>——>wifi router (in AP, access point, mode) and switch.  That way devices are getting ip’s from router (who get’s 1 ip straight from 
    - Consult: https://www.asus.com/us/support/FAQ/1015009/
    - Consult: http://www.tomshardware.com/answers/id-3776081/wifi-router-ethernet-devices-eachother.html
    - 

