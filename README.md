# Fern

Talos powered Kubernetes cluster controlled by FluxCD.

If you want to deploy something similar, have a look [here](https://github.com/onedr0p/cluster-template)

## Nodes

| Name   | Type               | SchematicId                                                      | CPU | RAM  |
|--------|--------------------|------------------------------------------------------------------|-----|------|
| Akame  | VM                 | a7bcadbc1b6d03c0e687be3a5d9789ef7113362a6a1a038653dfd16283a92b6b | 8   | 32GB |
| Felix  | Metal - UM773 Lite | 91de3ae80d181b2791d17a27d6374eaa2832c1726bf61d94e7266dd406c1a7a7 | 16  | 64GB |
| Nagumo | Metal - UM870 Slim | 91de3ae80d181b2791d17a27d6374eaa2832c1726bf61d94e7266dd406c1a7a7 | 16  | 96GB |

## Akame

```
customization:
    systemExtensions:
        officialExtensions:
            - siderolabs/qemu-guest-agent
            - siderolabs/util-linux-tools
```

## Felix & Nagumo

```
customization:
    extraKernelArgs:
        - cpufreq.default_governor=performance
    systemExtensions:
        officialExtensions:
            - siderolabs/amd-ucode
            - siderolabs/amdgpu
            - siderolabs/realtek-firmware
            - siderolabs/util-linux-tools
```

## Init

```bash
mise trust
pip install pipx
mise install

task bootstrap:init-secrets
# update .sops.yaml with the age public key
task bootstrap:init-sops
task bootstrap:talos
task bootstrap:apps
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
