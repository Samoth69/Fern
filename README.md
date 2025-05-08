# Fern

Talos powered Kubernetes cluster controlled by FluxCD.

If you want to deploy something similar, have a look [here](https://github.com/onedr0p/cluster-template)

## Nodes

## Akame

(Virtual machine)

`47f718feafaf5934d42735b54da7805fa768627393a4318a0c2d47fd5b1ecb79`

```
customization:
    systemExtensions:
        officialExtensions:
            - siderolabs/btrfs
            - siderolabs/qemu-guest-agent
            - siderolabs/util-linux-tools
```

## Init

```
mise trust
pip install pipx
mise install
```

## usefull commands

```bash
# force update flux
flux reconcile source git flux-system
# get flux objects status
flux get all -A --status-selector ready=false
# token for the dashboard
kubectl -n kubernetes-dashboard create token admin-user
# to connect to primary database
while true; do kubectl -n database port-forward "$(kubectl -n database get pods -l postgres-operator.crunchydata.com/role=master -o name)" 15432:5432; done
# hubble
cilium hubble port-forward&
hubble observe --verdict DROPPED -f
```
