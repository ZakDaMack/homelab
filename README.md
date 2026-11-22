# Zak's Homelab

blah blah blah

## Apps

- Traefik
- ArgoCD
- Longhorn
- Authentik
- OpenBao (WIP)
- Cert Manager
- Metrics Server
- Grafana
- Portfolio
- Homepage (WIP)
- Outline
- PaperlessNGX

## Installation

### Prereqs

- Talosctl
- Kubectl
- Helm
- Kustomize
- ArgoCD
- Kubeseal

### TalosOS

Talos is a blah [TODO]

Get a talos image from the [Talos Image Factory](https://factory.talos.dev/)

Generate the Talos configs with the fixed IP patch for `niflheim-cp-1` and the
scheduling patch that lets it run workloads alongside the control plane.
Generate `secrets.yaml` only for a new cluster. Reuse the original secrets when
regenerating configs for an existing cluster.

```bash
talosctl gen secrets -o 00-talos/secrets.yaml # new secrets, or copy an existing set
talosctl gen config YOUR_CLUSTER_NAME https://10.10.2.20:6443 \
  --with-secrets 00-talos/secrets.yaml \
  --config-patch-control-plane @00-talos/controlplane-network.patch.yaml \
  --config-patch-control-plane @00-talos/controlplane-scheduling.patch.yaml \
  --output 00-talos
```

Apply config to a node assigned `10.10.2.20`. Use current IP for initial apply.

```bash
talosctl apply-config --insecure --nodes 'CURRENT_NODE_IP' --file 00-talos/controlplane.yaml
talosctl config merge 00-talos/talosconfig # add the config into the default talosconfig
talosctl config endpoint 10.10.2.20 # set up the IP
talosctl bootstrap -n 10.10.2.20 # only run for a new cluster
```

Apply the cluster details provided by talos to the kubeconfig

```bash
talosctl kubeconfig -n 10.10.2.20 # create kubeconfig based on talosconfig
kubectl get nodes # verify it is running
```

### Bootstrapping

Install MetalLB and wait for its controller to start before applying the address pool and advertisement. 
The controller serves the webhook that validates those resources. The speaker advertises service IPs from this
cp.

```bash
git clone https://github.com/ZakDaMack/homelab.git
cd homelab/
kustomize build 01-bootstrap/metallb | kubectl apply -f - &&
  kubectl rollout status -n metallb-system deployment/controller --timeout=120s &&
  kubectl apply -f 01-bootstrap/metallb/00-metallb-config.yaml
```

Install ArgoCD, then wait for cluster to register the `Application` kind.

```bash
kustomize build 01-bootstrap/argocd | kubectl apply --server-side --force-conflicts -f - &&
  kubectl wait --for=condition=Established crd/applications.argoproj.io --timeout=120s
```

Install the [Sealed Secrets controller](#sealed-secrets) before enabling the app of apps while any applications still use `SealedSecret` resources. Apply the root Application to let ArgoCD discover and sync the apps that have automated sync enabled.
Use the local ArgoCD admin account if you need to sign in during bootstrap.
Configure Authentik SSO after its client secret is available in Kubernetes.

```bash
kubectl apply -f 01-bootstrap/root-application.yaml
kubectl -n argocd get applications
```

### Sealed Secrets

Currently, the app uses sealed secrets. Moving over to ESO.

```bash
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
helm upgrade --install sealed-secrets sealed-secrets/sealed-secrets \
  --namespace sealed-secrets --create-namespace --wait \
  --set-string fullnameOverride=sealed-secrets-controller
kubectl wait --for=condition=Established crd/sealedsecrets.bitnami.com --timeout=120s
```

Example
`kubeseal --scope namespace-wide -f <file> -w <output> --controller-name sealed-secrets-controller --controller-namespace sealed-secrets`

## TODO

- Apps (n8n/windmill, vaultwarden, fuelfinder (multi-deployment svc), mediaserver, CloudNativePG)
- Documentation
- Simplify bootstrapping (scripting?)
- Talos confs (add nvmes)
