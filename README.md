# Zak's Homelab

blah blah blah

## Apps

- Traefik
- ArgoCD
- Portfolio
- Longhorn
- Authentik
- Cert Manager
- Pocket ID (disabled)
- Metrics Server
- Grafana
- Homepage (WIP)

## Installation

### Prereqs

- Talosctl (iSCSI, Linux-util)
- Kubectl
- Helm
- Kustomize
- ArgoCD
- Kubeseal

### Bootstrapping

Clone the Git repo and run bootstrap to install the base packages before enabling the app of apps. Bootstrapped applications are specific to my Talos cluster and require install prior to setting up ArgoCD.

```bash
git clone https://github.com/ZakDaMack/homelab.git
cd homelab/
kustomize build 00-bootstrap | kubectl apply -f -
argocd login <ARGOCD_SERVER>
argocd app create apps --repo https://github.com/ZakDaMack/homelab.git --path apps --dest-server https://kubernetes.default.svc --dest-namespace argocd --sync-policy auto
argocd app sync apps
argocd app sync -l app.kubernetes.io/instance=apps
```

### Sealed Secrets

Currently, the app uses sealed secrets. It is installed via Helm.

```bash
helm create ns sealed-secrets
helm install sealed-secrets -n sealed-secrets --set-string fullnameOverride=sealed-secrets-controller sealed-secrets/sealed-secrets
```

Example
`kubeseal --scope namespace-wide -f <file> -w <output> --controller-name sealed-secrets-controller --controller-namespace sealed-secrets`

## TODO

- Grafana login authentik, grafana dashbaord access
- Crowdsec
- Firewall (Cilium?)
- Apps (windmill, vaultwarden, fuelfinder (multi-deployment svc), mediaserver, outline)
