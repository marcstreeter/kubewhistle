# Install Helm Chart Repository
To use helm with tiller (that may change in the near future) it must be [installed on your laptop and cluster](https://helm.sh/docs/using_helm/#installing-helm). The following commands and instructions are all run **from your laptop** that drives the entire process.

- Install helm on your laptop (that will be driving installs)
```bash
brew install kubernetes-helm
helm init --client-only
```
- Create Service Account on your cluster for Helm Tiller using [template](./manifests/helm-user.yaml)
```bash
kubectl apply -f ./manifests/helm-user.yaml
```
- Install tiller on your cluster using newly created service account/role
```bash
helm init --service-account tiller --history-max 200
```
- Validate Install (if it responds you're good to go)
```bash
helm version
```
*may take about 15 seconds to be ready*
- For further use you may want to review [it's big concepts](https://helm.sh/docs/using_helm/#three-big-concepts)
