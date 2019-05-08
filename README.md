# Kubewhistle
Simple guides to get you going to an onprem cluster that has many bells and whistles

# Node Preparation
Each kubernetes [node needs to be prepared](./README.node.md) for ssh connectivity, passwordless actions, etc.

# Install Kubernetes
From here you have the choice of installing from:
- Rancher [RKE](./README.rke.md)
- Kubespray [Ansible](./README.kubespray.md)

# Basics
- Enable Helm (may already have been done)
    - Install helm from [Helm README](./README.helm.md)
- Enable Rook
    - Install rook from [Rook README](./README.rook.md)