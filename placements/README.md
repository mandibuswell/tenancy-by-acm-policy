# placements/

ACM Placement rules that determine which clusters receive the generated policies.
The active placements are controlled by each subdirectory's `kustomization.yaml`.

See [capabilities/README.md](capabilities/README.md) for cluster capability labels.

For a full map of Argo apps, PolicySets, and placements, see
[docs/placements-cheatsheet.md](../docs/placements-cheatsheet.md).

## Policies placements (`placements/policies/`, namespace: `policies`)

| File | Placement name | Targets |
|---|---|---|
| `placement-hub.yaml` | `policies-placement-hub-clusters` | Hub (`local-cluster`) |
| `placement-managed-by-capability.yaml` | `policies-placement-managed-clusters` | `capability-container` **or** `capability-vm` (default) |
| `placement-managed-vm-capability.yaml` | `policies-placement-managed-vm-clusters` | `capability-vm` only |
| `placement-managed.yaml` | (legacy) | All spokes except hub |

## Tenancies placements (`placements/tenancies/`, namespace: `tenancies`)

| File | Name | Purpose |
|---|---|---|
| `placement-managed-by-capability.yaml` | `tenancies-placement-managed-clusters` | Tenant CR replication (capability OR) |
| `placement-hub.yaml` | `tenancies-placement-hub-clusters` | Hub only |

## How placements are used

- **Hub placement** — AC/SC hub resources (CRBs, MCRAs, Tenant CRD, Keycloak).
- **Managed placement** — CM/AC/SC spoke resources (namespaces, quotas, UDN, Tenant CR copies).
- **VM placement** — `kubevirt.io:*` and `acm-vm-extended:*` MCRAs on hub (see `hub-mcra-virt.yaml`).

## Switching strategy

In `placements/policies/kustomization.yaml`, replace capability files with a legacy
placement file if needed. See root [README.md](../README.md#cluster-placement).
