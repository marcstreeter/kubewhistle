# Install Helm Chart Repository
To use helm with tiller (that may change in the near future) it must be [installed on your laptop and cluster](https://helm.sh/docs/using_helm/#installing-helm).

- Install helm on your laptop (that will be driving installs)
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