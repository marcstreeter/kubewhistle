# Kubewhistle
Simple guides to get you going to an onprem cluster that has many bells and whistles

# The Layout

```bash
                                      .─. .─.                                   
                                   (   (   ).─.                               
                                 .─.`         .─.                             
                               .─.              .─.                           
                              (  .─   The Web      )                          
                               `(              .`─'                           
                                 `─(            )                             
                                    `─' (   )`─'                              
                                        │`─'                                  
                                        │                                     
                                        │                                     
                                        ▼                                     
                              ┏━━━━━━━━━━━━━━━━━━━┓                           
                              ┃      Router       ┃                           
                              ┃(DMZ exposing RPi) ┃                           
                              ┗━━━━━━━━━━━━━━━━━━━┛                           
                                        │                                     
                                        │                                     
                                        │                                     
┌─workers────┐                          ▼                                     
│ ┏━━━━━━━━┓ │                ┏━━━━━━━━━━━━━━━━━━━┓             ┌─masters────┐
│ ┃ NODE 2 ┃ │                ┃   ┌ ─ ─ ─ ─ ─ ┐   ┃             │            │
│ ┗━━━━━━━━┛ │ ports: 80, 443 ┃    HAProxy LB4    ┃ ports: 6443 │ ┏━━━━━━━━┓ │
│ ┏━━━━━━━━┓ │◀───────────────┃   └ ─ ─ ─ ─ ─ ┘   ┃────────────▶│ ┃ NODE 1 ┃ │
│ ┃ NODE 3 ┃ │                ┃                RPi┃             │ ┗━━━━━━━━┛ │
│ ┗━━━━━━━━┛ │                ┗━━━━━━━━━━━━━━━━━━━┛             │            │
└────────────┘                                                  └────────────┘                                
```

# Node Preparation
Each kubernetes [node needs to be prepared](./README.node.md) for ssh connectivity, passwordless actions, etc.

# Install Kubernetes
From here you have the choice of installing from:
- Rancher [RKE](./README.rke.md)
- Kubespray [Ansible](./README.kubespray.md)

# Basics
- Enable Dashboard
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard.yaml
```
*Use `kubectl proxy` before [accessing it](http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login)*
- Enable Helm (may already have been done)
    - Install helm from [Helm README](./README.helm.md)
- Enable Rook
    - Install rook from [Rook README](./README.rook.md)
