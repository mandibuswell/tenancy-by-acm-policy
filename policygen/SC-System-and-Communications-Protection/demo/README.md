# Demo-only hub policies

Workshop clusters with `keycloak-system` franchise SSO enable this PolicyGenerator via:

```bash
oc apply -f argocd/application-tenancy-sc-hub-demo.yaml
# or uncomment the line in argocd/apply.sh
```

## What it does

- `tenancy-hub-keycloak-legacy-demo-realms` — creates Keycloak realms for Tenant CRs **without** `spec.identity` (starwars, startrek, etc.)

## What stays outside Argo

- Custom CSS themes and Keycloak pod volume mounts — `apply-themes.sh` in demo-setups
- Production identity tenants — use main SC policy with `spec.identity` (see keycloak/README.md)

Production multi-tenant hubs should **not** enable this Application.
