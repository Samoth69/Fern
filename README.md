# Fern

Talos powered Kubernetes cluster controlled by FluxCD.

If you want to deploy something similar, have a look [here](https://github.com/onedr0p/cluster-template)

## Nodes

| Name   | Type               | CPU | RAM  | SchematicId                                                      |
|--------|--------------------|-----|------|------------------------------------------------------------------|
| Akame  | VM                 | 10  | 64GB | ef973c8091be093db8b9cf4829e7f430f8e931b744cbe31adb842ec713df9339 |
| Felix  | Metal - UM773 Lite | 16  | 64GB | 35d1f97b3aff9e464c22efbab1b2e71667df933f7bdc57f28321eee35a26ef05 |
| Nagumo | Metal - Intel 7500 | 4   | 16GB | f313c40c3af72e3c22303abeb8faac8d02769e1ef4ef66aa4423bf6744f01e94 |

## Akame

```
customization:
    extraKernelArgs:
        - ipv6.disable=1
    systemExtensions:
        officialExtensions:
            - siderolabs/btrfs
            - siderolabs/gvisor
            - siderolabs/qemu-guest-agent
            - siderolabs/util-linux-tools
```

## Felix

```
customization:
    extraKernelArgs:
        - cpufreq.default_governor=performance
        - ipv6.disable=1
    systemExtensions:
        officialExtensions:
            - siderolabs/amd-ucode
            - siderolabs/amdgpu
            - siderolabs/gvisor
            - siderolabs/realtek-firmware
            - siderolabs/util-linux-tools
```

## Nagumo

```
customization:
    extraKernelArgs:
        - cpufreq.default_governor=performance
        - ipv6.disable=1
    systemExtensions:
        officialExtensions:
            - siderolabs/gvisor
            - siderolabs/i915
            - siderolabs/intel-ucode
            - siderolabs/mei
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
