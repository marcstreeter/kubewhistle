# Install Istio (using Helm)
Assumes you've already enabled Helm

- Pull down project and [checkout correct version](https://github.com/istio/istio/releases)
```
git clone https://github.com/istio/istio.git
cd istio
git checkout tags/1.1.6
```
- Install init containers
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



ISTIO NEXT STEPS
- Deploy sample app
    - Label namespace for automatic sidecar injection
kubectl label namespace default istio-injection=enabled

