# finpay-deploy

Deployment charts and manifests for **FinPay** — a fictional mid-size fintech company used in the [Octopus Deploy Enterprise Lab](https://github.com/TODO_ORG/octopus-enterprise-lab).

## Structure

```
├── charts/
│   ├── payments-api/        # Core payments service (Helm)
│   ├── fraud-detector/      # ML fraud detection (Helm)
│   └── merchant-portal/     # Merchant-facing portal (Helm)
├── manifests/
│   └── kyc-service/         # KYC identity verification (raw K8s YAML)
└── argocd-manifests/
    └── payments-api/        # ArgoCD-managed manifests
        ├── development/
        ├── staging/
        └── production/
```

## Services

| Service | Type | Octopus Space | Description |
|---------|------|---------------|-------------|
| `payments-api` | Helm chart | Payments | Core payment processing. Canary deploys, production approval gates. |
| `fraud-detector` | Helm chart | Payments | ML-based fraud detection. Higher resource requirements. |
| `merchant-portal` | Helm chart | Merchants | Merchant-facing portal. Tenanted (per-customer) deployments. |
| `kyc-service` | Raw YAML | Merchants | Identity document processing. Handles PII, stricter compliance. |

All services use `nginx:1.25-alpine` as the container image. Charts are structured like real production services (per-environment values, HPA, PDB, health checks, liveness/readiness probes).

## Per-Environment Values

Each Helm chart includes environment-specific values files:

- `values.yaml` — base/default values
- `values-development.yaml` — lower resources, debug logging
- `values-staging.yaml` — moderate resources, info logging
- `values-production.yaml` — full resources, HPA enabled, PDB, warn logging

## Raw YAML (kyc-service)

The KYC service uses Octopus variable substitution (`#{VariableName}`) instead of Helm. Variables like `#{Replicas}`, `#{LogLevel}`, and `#{DocumentStorageUrl}` are replaced by Octopus at deploy time.

## ArgoCD Manifests

The `argocd-manifests/` directory contains plain Kubernetes YAML organized per environment. These are used with Octopus's ArgoCD integration — Octopus commits image tag updates to this directory, and ArgoCD syncs the cluster.

## Getting Started

This repo is a companion to the [Octopus Deploy Enterprise Lab](https://github.com/TODO_ORG/octopus-enterprise-lab). Fork it, then follow the lab guide.
