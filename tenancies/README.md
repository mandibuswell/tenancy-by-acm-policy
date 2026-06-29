# Tenant CRs

Sample tenants are **not** applied from this directory anymore.

Create tenants via the **Create Tenant** console plugin (`/tenant-create`) or `oc apply -f` on a manifest you author yourself.

Reference examples (not auto-deployed):

- [`examples/tenant-starwars.yaml`](../examples/tenant-starwars.yaml)
- [`examples/tenant-startrek.yaml`](../examples/tenant-startrek.yaml)
- [`examples/tenant-gigashadow-identity.yaml`](../examples/tenant-gigashadow-identity.yaml)

Each Tenant CR provisions a **workload namespace** on managed clusters (`spec.workloadNamespace`, defaulting to the tenant name) with label `tenant: <name>`.

**Argo CD `tenancy-base`** syncs the `tenancies/` directory. It is **empty by default** (no auto-deployed starwars/startrek). If your cluster still recreates sample tenants, check that `tenancy-base` tracks your fork/branch — run `argocd/apply.sh` from this repo so `repoURL` and `targetRevision` match your clone.
