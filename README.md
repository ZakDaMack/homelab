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
cd homelab/
kustomize build bootstrap | kubectl apply -f -
argocd login <ARGOCD_SERVER>
argocd app create apps --repo https://github.com/ZakDaMack/homelab.git --path apps --dest-server https://kubernetes.default.svc --dest-namespace argocd --sync-policy auto
argocd app sync apps
argocd app sync -l app.kubernetes.io/instance=apps
```

## TODO

- Dashboard
- Metrics (node exporter, logging, prometheus, grafana, loki)
- Auth (Authelia/Authentik?)
- Firewall (Cilium?)
- Storage (Longhorn)
- SecretsManager (how to store?)
- Apps (windmill, vaultwarden, fuelfinder (multi-deployment svc), mediaserver, outline)
- Databases?
