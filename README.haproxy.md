# HAProxy
This is our load balancer for onprem. Note: **there probably is a better way**. 

# TLDR
#### Enable HAProxy
You want to make sure that HAProxy turns on after rebooting your server

#### Configure HAProxy
Set your `/etc/haproxy/haproxy.cfg` to the contents of the [template](./templates/haproxy.cfg) and restart haproxy
```
sudo systemctl restart haproxy
```

# Why

#### Proxy requests to your cluster's main ports 80/443 to your Ingress Controller
In order to proxy requests from your L4 load balancer to your layer 7 loadbalancer (application level) we'll be updating our HAProxy config file, `/etc/haproxy/haproxy.cfg`, and also applying a Config Map to our Nginx Ingress Controller. Much of the process [comes from this guide](https://itnext.io/cluster-recipe-external-proxy-for-kubernetes-ingress-or-docker-compose-ingress-with-haproxy-on-f81e3adee5ef)

- Add sections for frontend/backend for both secure/insecure traffic
```
frontend http_front
  mode tcp
  bind *:80
  default_backend http_back

frontend https_front
  mode tcp
  bind *:443
  default_backend https_back

backend http_back
  mode tcp
  server s1 192.168.1.201:80 send-proxy

backend https_back
  mode tcp
  server s1 192.168.1.202:443 send-proxy
```

- RKE already installed Nginx as a loadbalancer and it needs an extra option, `use-proxy-protocol`, as shown in the [template](./manifests/nginx-config-map-for-haproxy.yaml) to proxy requests, so you apply it
```
kubectl apply -f ./manifests/nginx-config-map-for-haproxy.yaml
```

#### Proxy requests to your cluster's KubeAPI so that you can use kubectl remotely
Is this safe? I'm not sure, in fact, I doubt it.  Does it work, yes it does.  Do I recommend it? I don't recommend any of this. I recommend you use a managed kubernetes service from the likes of the big three (AWS, Azure, Google) and NOT this! OK?!  That said here we go!
- Add section  to your HAProxy config so that requests for this port go to the API Server on your master node(s) 
```
frontend dashboard
    bind *:6443
    mode tcp
    option tcplog
    default_backend masternode

backend masternode
    mode tcp
    option tcplog
    option tcp-check
    balance roundrobin
    server kubemaster 192.168.1.211:6443 check
    http-request set-header X-Forwarded-Port %[dst_port]
    http-request add-header X-Forwarded-Proto https if { ssl_fc }
```

# Helpful resources
Some places that helped me a little bit
- https://kubernetes.io/docs/concepts/services-networking/
- https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-ingress-guide-nginx-example.html
