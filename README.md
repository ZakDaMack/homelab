# Zak's Homelab

blah blah blah

## Apps

- Traefik
- ArgoCD
- Portfolio
- Dashboard (WIP)

## Installation

### Prereqs

- Talosctl
- Kubectl
- Kustomize
- ArgoCD

### Bootstrapping

Clone the Git repo and run bootstrap to install the base packages before enabling the app of apps. Bootstrapped applications are specific to my Talos cluster and require install prior to setting up ArgoCD.

```bash
git clone https://github.com/ZakDaMack/homelab.git

```


### Init TalosOS

1. Init MetalLB CRDs `kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml`
2. Setup local conf `kubectl apply -f https://raw.githubusercontent.com/ZakDaMack/homelab/refs/heads/main/installation/00-metallb-config.yaml`
3. [TODO] firewall

### Setup ArgoCD

1. `kustomize build https://raw.githubusercontent.com/ZakDaMack/homelab/refs/heads/main/argocd/installation | kubectl apply -f -`
2. `kubectl config set-context --current --namespace=argocd`
3. `argocd app create apps --repo https://github.com/ZakDaMack/homelab.git --path apps --dest-server https://kubernetes.default.svc --dest-namespace default --sync-policy auto`
4. `argocd app sync -l app.kubernetes.io/instance=app`

## TODO

- Simplify bootstrapping
- Dashboard
- Metrics (node exporter, logging, prometheus, grafana, loki)
- Auth (Authelia/Authentik?)
- Firewall (Cilium?)
- Storage (Longhorn)
- SecretsManager (how to store?)
- Apps (windmill, vaultwarden, fuelfinder (multi-deployment svc), mediaserver, outline)
- Databases?
