# Fern

Talos powered Kubernetes cluster controlled by FluxCD.

If you want to deploy something similar, have a look [here](https://github.com/onedr0p/cluster-template)

## Nodes

| Name   | Type                                   | SchematicId                                                      | CPU | RAM  |
|--------|----------------------------------------|------------------------------------------------------------------|-----|------|
| Akame  | VM                                     | c9b3b8505d39208762ec98b18bb676b8081ff4a43db792f45a17909c33f57140 | 8   | 32GB |
| Felix  | Metal - UM773 Lite                     | eba480ee0288b10f6e409a54fde3e4bc425f18f56efbd12bf2a343a9334517fd | 16  | 64GB |
| Nagumo | Metal - ~~UM870 Slim~~ Aoostar WTR PRO | eba480ee0288b10f6e409a54fde3e4bc425f18f56efbd12bf2a343a9334517fd | 16  | 96GB |

## Akame

```
customization:
    extraKernelArgs:
        - ipv6.disable=1
    systemExtensions:
        officialExtensions:
            - siderolabs/btrfs
            - siderolabs/qemu-guest-agent
            - siderolabs/util-linux-tools
```

## Felix & Nagumo

```
customization:
    extraKernelArgs:
        - cpufreq.default_governor=performance
        - ipv6.disable=1
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
# test config
flux-local test --path kubernetes/flux/cluster --enable-helm --all-namespaces -v
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
