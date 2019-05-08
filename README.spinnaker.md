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
    