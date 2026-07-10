# Tenant console portal (VMaaS + Developer)

Hub policies that control **which console perspectives** tenant IdP groups see, independent of Fleet Management and platform admin views.

## Design

Each portal capability uses a **marker ConfigMap** in `tenancies` and a **RoleBinding** per tenant tier group. Console SAR checks the marker **and** excludes platform admins (`missing: clusteroperators list`), so cluster-admin wildcard access does not expose tenant perspectives.

| Marker | Role | Bound when `workloadProfile` | Console effect |
|--------|------|------------------------------|----------------|
| `portal-vmaas` | `tenant-portal-vmaas` | `vms`, `both` | VMaaS plugin perspective (default landing) |
| `portal-developer` | `tenant-portal-developer` | `containers`, `both` | OpenShift **Developer** perspective (non-admins only) |

**Split from Fleet Management:** `acm` (Fleet Management) requires `clusteroperators` list (platform only). Tenants keep `acm-vm-fleet:view` for fleet VM API/search; portal visibility is separate.

## Files

| File | Purpose |
|------|---------|
| `console/tenant-portal-markers.yaml` | ConfigMaps + Roles |
| `console/policy-console-perspective-rbac-vmaas.yaml` | Perspective visibility rules |
| `acm-finegrained-rbac/hub-tenant-console-rbac.yaml` | Per-tenant RoleBindings from Tenant CRs |

## Workload profiles

| Profile | VMaaS default | Developer | Fleet API (`acm-vm-fleet:view`) |
|---------|---------------|-----------|-----------------------------------|
| `vms` | Yes | No | Yes |
| `containers` | No | Yes | No |
| `both` | Yes (default) | Yes | Yes |

## Future: tenant admin observability

Add `portal-observability` ConfigMap + Role + bindings (e.g. only `*-tenant-admin` groups), then expose nav in `tenant-vmaas-gui` gated on the same SAR pattern.

## Rollback

Swap `policy-console-perspective-rbac-vmaas.yaml` back to `policy-console-perspective-rbac.yaml` in `policygenerator-hub.yaml` and remove `hub-tenant-console-rbac.yaml` from AC hub generator.
