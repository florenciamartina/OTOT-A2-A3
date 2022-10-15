- Install all resources needed for metrics-server with `kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`
- For the TLS to be working, edit the Deployment manifest by using the command `kubectl -nkube-system edit deploy/metrics-server` to add a flag `--kubelet-insecure-tls` to `deployment.spec.containers[].args[]`
- Run `kubectl -nkube-system rollout restart deploy/metrics-server` to restart the Deployment
- Add a new file `backend-hpa.yaml` and apply it with `kubectl apply -f k8s/manifests/k8s/backend-hpa.yaml`
- After applying it, load test the backend by opening and refreshing the page `http://localhost/app` repetaedly to stress the servers.
- Run `kubectl get po` and `kubectl describe hpa` to see the scale

- Check the nodes with the command `kubectl get nodes -L topology.kubernetes.io/zone`
- Add the topologySpread Constraints which checks the number of Pods against the node label "topology.kubernetes.io/zone" and make sure for sets of nodes of different values (e.g. a, b), the maxSkew, i.e. maximum difference, will be 1.
- To add this, we create a new file `backend-deployment-zone-aware.yaml` with 10 replicas and apply it with `kubectl apply -f k8s/manifests/k8s/backend-deployment-zone-aware.yaml`

- Run `kubectl get po -lapp=backend-zone-aware -owide --sort-by='.spec.nodeName'` to check that the pods are distributed evenly across the two zones, zone a and zone b
