Firstly, I deployed a k8s cluster

- To create my own cluster, I use the command `kind create cluster --name kind-1 --config k8s/kind/cluster-config.yaml`
- To verify that my cluster is running, I can ran either `docker ps` to get information about my k8s nodes

After creating the cluster, I ran `kubectl cluster-info` to ensure :

- My control plane is running in `localhost` or `127.0.0.1`
- The port matches `kind-control-plane` container's exposed port which was `44819` for me

I pushed my A1 Docker image to the Docker Hub as a public repository

- I go to [Docker Hub](https://hub.docker.com) and sign in with my credentials
- I clicked the button `Create repository` and filled in my username and repo name, choose public as the visibility and click `Create`
- I went to the terminal, and input the following commands
  - `docker images` to get the names of my local docker images
  - I choose an image name that I want to push to the repo, let's call it `<image-name>`
  - I then tagged the image with this command `docker-tag <image-name> <my-username>/<my-repo-name>`
  - `docker login` to login to the docker and push to the repo by doing `docker push <my-username>/<my-repo_name>`

After that, I deployed my A1 Docker image as a Deployment with 3 replicas exposed by a Service object

- I added the `backend-deployment.yaml` file which contains Deployment manifest, change the `image ` field to `<my-username>/<my-repo-name>`
- I ran `kubectl apply -f k8s/manifests/k8s/backend-deployment.yaml`

To confirm that the deployment was successful, I ran both :

- `kubectl get deployment/backend --watch`
- `kubectl get po -lapp=backend --watch` to check individual container

After confirming that the deployment was a success, I created Ingress controller by running `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml`. I then confirmed that it is ready by running `kubectl -n ingress-nginx get deploy -w`.

To setup the service, I added the `backend-service.yaml` file which is the Service manifest. After that I ran `kubectl apply -f k8s/manifests/k8s/backend-service.yaml` to apply the service manifest. I confirm the service is running with command `kubectl get svc`.

Lastly, I created an Ingress object, I created a new file `backend-ingress.yaml`. Just like the others, I also applied the manifest by running `kubectl apply -f k8s/manifests/k8s/backend-ingress.yaml`. To confirm that the ingress is running, I use the command `kubectl get ingress`.
