# RKE
Here we take the Rancher route

# Prepare Node and Host
- Prepare Host (the laptop you'll use to control your nodes)
    - install RKE 
    ```
        brew install rke
    ```
    *NOTE: you can check the current version of RKE [available from brew](https://formulae.brew.sh/formula/rke)*
    - generate your cluster config
    ```
    rke --config
    ```
    or [use prepared template](./templates/rke-cluster.yaml)
- Prepare Nodes (where kubernetes will run)
    - Install Docker (consult install reference)[https://docs.docker.com/install/linux/docker-ce/ubuntu/] and (post install refernce)[https://docs.docker.com/install/linux/linux-postinstall/] according to (node requirements)[https://rancher.com/docs/rancher/v2.x/en/installation/requirements/]
        ```
        # TLDR
        sudo apt-get update
        sudo apt-get install \
             apt-transport-https \
             ca-certificates \
             curl \
             gnupg-agent \
             software-properties-common
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
        sudo add-apt-repository \
             "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
             $(lsb_release -cs) \
             stable"
        sudo apt-get update
        sudo apt-get install docker-ce docker-ce-cli containerd.io
        sudo usermod -aG docker $USER
        # NEED TO OPEN NEW TERMINAL TO TAKE EFFECT
        # Ensure installed right version: Ubuntu 18.04 (64-bit) works with Docker 18.09.x
        ```

# Install Kubernetes
Running the following installs kubernetes

```
rke up --config ./rke-cluster.yml  # or whatever you called your generated cluster yaml file
```

RKE Creates a resulting config file in your cwd that you need to cp to your `~/.kube/config` like so

```
cp ./kube_config_rancher-cluster.yml ~/.kube/config
```

Update your `~/.kube/config` file by changing the following `server: "https://192.168.1.211:6443"` to `server: "https://kubernetes:6443"`
and add `192.168.1.211 kubernetes` to your `/etc/hosts` file (this could also be your public ip if you are forwarding traffic via HAProxy, for example)

# Install Kubernetes Dashboard
The dashboard is not installed by default but can be installed easily with
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard.yaml
```
You would then [access it](http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login) after running
```
kubectl proxy
```

# Install Helm Chart Repository
Currently uses helm with tiller (that may change in the near future) and so it [must be installed](https://helm.sh/docs/using_helm/#installing-helm).

- Install helm on your local computer (that will be driving installs)
```
brew install helm
helm init --client-only
```
- Create Service Account ony your cluster for Helm Tiller using (template)[./manifests/helm-user.yaml]
```
kubectl apply -f helm-user.yaml
```
- Install tiller on your cluster using newly created service account/role
```
helm init --service-account tiller --history-max 200
```
- Validate Install (if it responds you're good to go)
```
helm version
```
- For further use you may want to review [it's big concepts](https://helm.sh/docs/using_helm/#three-big-concepts)

- Add helm Chart respository for rancher
```
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```
- Add Cert manager (will be using self-signed certs from Rancher's CA)
```
helm install stable/cert-manager \
  --name cert-manager \
  --namespace kube-system \
  --version v0.5.2
```
- Install Rancher
```
helm install rancher-stable/rancher \
  --name rancher \
  --namespace cattle-system \
  --set hostname=rancher.kube.watch
```
- Make sure that [HAProxy Forwarding is already set up](./README.haproxy.md)
- Should be able to hit the site without problems 
    - https://rancher.kube.watch/
- After setting up your admin username/password it will take about 10 minutes for the cluster to go to a ready state

# Prepare for Rook

You've already been prepared for rook with the following section in your [rancher-cluster.yaml](./templates/rancher-cluster.yaml) file per instructions [from rook.io](https://rook.io/docs/rook/v1.0/flexvolume.html#platform-specific-flexvolume-path):
```
services:
  kubelet:
    extra_args:
      volume-plugin-dir: /usr/libexec/kubernetes/kubelet-plugins/volume/exec
    extra_binds:
      - /usr/libexec/kubernetes/kubelet-plugins/volume/exec:/usr/libexec/kubernetes/kubelet-plugins/volume/exec
```
Since this is the default location there shouldn't be anything else to do.


