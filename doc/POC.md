# AsciiArtify PoC

Since I've decided to go with a Minikube in the previous hometask, I need to make a PoC on a Minikube cluster.

### Install minicube

Go to the https://minikube.sigs.k8s.io/docs/start/ and choose your platrofm. Install the Minikube.

Start it:

```
minikube start
```

It will launch the Minikube instance on your enviroment.

I was using Google Codespace and VS Code IDE:

![minikube-1] (https://github.com/ottomen/AsciiArtify/tree/main/doc/images/minikube-1.png)

### Install Argo CD

Create k8s namespace, create and updates resources:

![argocd-1] (https://github.com/ottomen/AsciiArtify/tree/main/doc/images/argocd-1.png)

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Change app service type to LoadBalancer:

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Make port forwarding:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Get default pass, copy it, login, and change it to a new one:

```
argocd admin initial-password -n argocd
argocd login 127.0.0.1:8080
argocd account update-password
```

### Create guestbook

```
kubectl config set-context --current --namespace=argocd
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
```

Visit [https://127.0.0.1:8080/](https://127.0.0.1:8080/) and enter login (admin by default) and you new password.

![argocd-2] (https://github.com/ottomen/AsciiArtify/tree/main/doc/images/argocd-2.png)

You can check the Guestbook status:

```
argocd app get guestbook
```

### Minikube

You can check the Minikube dashboard:

```
minikube dashboard
```

![minikube-2] (https://github.com/ottomen/AsciiArtify/tree/main/doc/images/minikube-2.png)
