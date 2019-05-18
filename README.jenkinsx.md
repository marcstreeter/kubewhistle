Jenkins X OnPrem (not fully baked - still have to use )
- Note: https://github.com/jenkins-x/jx/issues/1434 
- Makes Jenkins X work
- Install jx 
  ```bash
  brew tap jenkins-x/jx
  brew install jx
  ```
- Using helm for ingress controller:
  ```bash
  helm install --name nginx-ingresser stable/nginx-ingress \
  --set controller.service.type=NodePort \
  --set controller.service.externalIPs={192.168.1.201,192.168.1.202,192.168.1.203} \
  --set controller.service.externalTrafficPolicy=Local
  ```
- I'm still trying to get this to work (currently not working)
```
jx install --provider=kubernetes \
    --external-ip=192.168.1.211 \
    --ingress-service=default-http-backend \
    --ingress-deployment=default-http-backend \
    --ingress-namespace=ingress-nginx \
    --domain=kube.watch
```

```
jx install --provider=kubernetes \
--skip-ingress \
--external-ip=192.168.1.211 \
--domain=kube.watch
```

Other Notes
- had an issue installing once, it was giving me `401` errors. I uninstalled `jx` and also removed the `~/.jx` folder. I made sure that all the repo's I had created previously through jenkins-x were removed from my github account and also removed all the [personal access tokens](https://github.com/settings/tokens) that I had generated. In that way it prompted me for access again (providing a link to create the access token)

NEXT STEPS
- Jenkins X
    - https://www.youtube.com/watch?v=iytHDaLb3-Q
    - https://jenkins.io/projects/jenkins-x/
    - gitlab
        - https://github.com/jenkins-x/jx/issues/3363
        - https://github.com/jenkins-x/jx/issues/2585
        - https://github.com/jenkins-x/jx/issues/3338
        - https://github.com/jenkins-x/jx/issues/445
        - https://github.com/jenkins-x/jx/issues/930
        - 

