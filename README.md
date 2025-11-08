# Zak's Homelab

blah blah blah

## Apps

-

## Installation

### Prereqs

- Talosctl
- Kubectl
- Kustomize
- ArgoCD

### Bootstrapping


### Init TalosOS

1. Init MetalLB CRDs `kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml`
2. Setup local conf `kubectl apply -f https://raw.githubusercontent.com/ZakDaMack/homelab/refs/heads/main/installation/00-metallb-config.yaml`
3. [TODO] firewall

### Setup ArgoCD

1. `kustomize build https://raw.githubusercontent.com/ZakDaMack/homelab/refs/heads/main/argocd/installation | kubectl apply -f -`
2. `kubectl config set-context --current --namespace=argocd`
3. `argocd app create apps --repo https://github.com/ZakDaMack/homelab.git --path apps --dest-server https://kubernetes.default.svc --dest-namespace default`

## TODO

- Dashboard
- Metrics (node exporter)
- Auth (Authelia/Authentik?)
- Firewall (Cilium?)
- Storage (Longhorn)
- SecretsManager (how to store?)
