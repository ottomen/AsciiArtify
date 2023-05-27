# AsciiArtify MVP

## Task

With the successful completion of the PoC, the "AsciiArtify" team is ready to move on to the MVP deployment.

An MVP is a minimal product with the basic features needed to test the product on a focus group of users. At this stage, developers add additional features, fix bugs, and improve the product based on user feedback.

From the DevOps side, you need to create an application in ArgoCD that will monitor the Git repository of the product https://github.com/den-vasyliev/go-demo-app and configure automatic synchronization.

After successfully configuring ArgoCD, you can run a full loop to demonstrate how ArgoCD automatically tracks and synchronizes changes from a Git repository and deploys them to a Kubernetes cluster.

The result of the task will be a short demo in the project repository, which demonstrates how the application works, deployed using the infrastructure you have configured.

An example of settings can be found in the Coding Session video.

The answer to the task will be a link to the AsciiArtify repository with an embedded demo that demonstrates the product's operation on your deployed infrastructure (doc/MVP.md file in Markdown format, main branch)

## Result

Here is the demo of installation:

[![asciicast](https://asciinema.org/a/9ds8ZXwUpgjPGNcPmOFvX4kO4.svg)](https://asciinema.org/a/9ds8ZXwUpgjPGNcPmOFvX4kO4)

1. Installing Minikube on the running Github Codespace:

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

2. Starting cluster:

```
minikube start
```

3. Adding adn setting up the Argo-CD

**Install**:

```
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

**Setup**:

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl port-forward svc/argocd-server -n argocd 8080:443

argocd admin initial-password -n argocd
argocd login 127.0.0.1:8080
argocd account update-password

```

4. Adding Demo project

[![asciicast](https://asciinema.org/a/bSNamKeQaapoOXqonWJnyibJU.svg)](https://asciinema.org/a/bSNamKeQaapoOXqonWJnyibJU)

Re-done with a new 4 CPUs cloud env, because 2 CPUs are not enough:

![redo](https://github.com/ottomen/AsciiArtify/blob/main/doc/images/redo.png?raw=true)

```
kubectl config set-context --current --namespace=demo
argocd app create demo --repo https://github.com/den-vasyliev/go-demo-app.git --path helm --dest-server https://kubernetes.default.svc --dest-namespace default
```

5. Visiting the ArgoCd dashboard:

```
127.0.0.1:8080
```

![mvp-1](https://github.com/ottomen/AsciiArtify/blob/main/doc/images/mvp-1.png?raw=true)

6. Testing the service:

```
kubectl port-forward -n default svc/ambassador 8088:80
curl localhost:8088
wget -O /tmp/p.jpg https://avatars.githubusercontent.com/u/2145808?v=4
curl -F 'image=@/tmp/p.jpg' localhost:8088/img/
```

![payload](https://github.com/ottomen/AsciiArtify/blob/main/doc/images/payload.png?raw=true)

7. Syncing

ArgoCD app should be synced when the repo main branch will be updated. That's why I'm doing:

```
argocd app set demo --sync-policy automated
```

![final](https://github.com/ottomen/AsciiArtify/blob/main/doc/images/final.png?raw=true)