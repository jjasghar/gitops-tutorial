# Simple installataion of argocd

## Scope

This is the simple installation of ArgoCD mainly focused for vanilla Kubernetes.

## Steps

- Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

- If needed, install the ArgoCD CLI

```bash
brew install argocd
```

- Either expose the ArgoCD API server via a loadbalancer or port forward.

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

# or 

kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- Login via the CLI

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
argocd account update-passord
```

- Create an example application from a git repository

```bash
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
```
