# Keycloak realm policy

The `tenancy-hub-keycloak-realms` policy (hub only) creates a `KeycloakRealmImport`
for every `Tenant` CR in the `tenancies` namespace. The template lives in
`realm-import-from-crd.yaml` and is evaluated at policy sync time using `lookup`.

## Opt-in console SSO (`spec.identity`)

Tenants **without** `spec.identity` continue to receive Keycloak realm imports (legacy
demo behaviour). New tenants should set `spec.identity.enabled: true` to opt in to
full console SSO automation:

| `provider` | Policy behaviour | IdP registration |
|------------|------------------|------------------|
| `keycloak` | `KeycloakRealmImport` on chosen Keycloak CR | CronJob adds `{tenant}-idp` to `oauth/cluster` |
| `oidc` | No realm import | CronJob registers IdP; `status.identity` lists external setup notes |

Required fields when enabled:

- `clientSecretRef` — Secret in `openshift-config` (create via form or CLI; never store the value on the CR)
- `keycloak.namespace` / `keycloak.instanceName` — which Keycloak CR to use (demo default: `keycloak-system/main`)

See [`examples/tenant-gigashadow-identity.yaml`](../../../examples/tenant-gigashadow-identity.yaml).

## Seed users

Each tenant realm is bootstrapped with three default users. Usernames use an
email-style local part so they align with the `apply-themes.sh` onboarding format
used in the ACM Keycloak demo.

| Username | Group | Password | Notes |
|----------|-------|----------|-------|
| `admin@<tenant>.local` | `<tenant>-tenant-admin` from the Tenant CR | `password` | Admin tier |
| `user@<tenant>.local` | `<tenant>-tenant-user` | `password` | Edit tier |
| `viewer@<tenant>.local` | `<tenant>-tenant-viewer` | `password` | Omitted if `viewerGroup` is unset |

Example for tenant `starwars`:

- `admin@starwars.local` / `password` → group `starwars-tenant-admin`
- `user@starwars.local` / `password` → group `starwars-tenant-user`
- `viewer@starwars.local` / `password` → group `starwars-tenant-viewer`

These users are **demo bootstrap accounts only**. Replace them with production
identities (LDAP, OIDC federation, or Vault-generated credentials) before any
real deployment.

## OIDC client and OpenShift login

Each realm includes an `openshift-<tenant>` client with:

- Redirect URI: `https://oauth-openshift.<cluster-domain>/oauth2callback/<tenant>-idp`
- Console wildcard: `https://console-openshift-console.<cluster-domain>/*`
- Group membership mapper (`groups` claim, `full.path: false`)

A matching client secret must exist in `openshift-config` and an IdP entry must
be added to `oauth/cluster` before console SSO works. See the demo
`acm-tenancy-keycloak` use case for the full handshake.

## Custom login themes

The template sets `loginTheme` to the tenant name (`bsg` when the tenant is
`battlestar`). Custom CSS themes are **not** deployed by this policy — mount
theme ConfigMaps separately or use `apply-themes.sh` for theme-only realms.

**Do not** run themed realm imports for tenants that have a `Tenant` CR while
this policy uses `remediationAction: enforce`; ACM will revert manual changes on
the next sync. Use separate realm names for themed SSO demos (for example
`mandalorian` instead of `starwars`).

## Further work

See [TODO.md](TODO.md) for client-secret lookup, OAuth IdP automation, and
production hardening items.
