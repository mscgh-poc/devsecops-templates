# devsecops-templates

Workflows reutilizables para la organización **mscgh-poc**.

## Workflows disponibles

| Workflow | Tipo | Descripción |
|---|---|---|
| `cd.yml` | Reusable (orquestador) | Cadena completa: CI + Build + Deploy |
| `ci.yml` | Reusable | Tests + análisis GHAS |
| `security-analysis.yml` | Reusable | CodeQL SAST + Dependency Review |
| `build-push.yml` | Reusable | Build Docker y push a ACR |
| `deploy-aks.yml` | Reusable | Despliegue a AKS con OIDC |

## Modelo de ramas

```
feature/* --> develop --> main
```

- **feature → develop**: PR con análisis GHAS obligatorio
- **develop → main**: PR con aprobación del equipo `approvers`
- **main**: Rama protegida, despliegue a producción

## Uso desde un microservicio

El caller del mic declara solo evento, permisos, un job `uses`, `with` y secretos.
La orquestación (CI -> build -> deploy) queda en templates.

```yaml
# Caller de PR: una sola llamada
jobs:
  analysis:
    uses: mscgh-poc/devsecops-templates/.github/workflows/ci.yml@main
    with:
      node-version: '22'
```

```yaml
# Caller de deploy: una sola llamada al orquestador
jobs:
  deploy-dev:
    uses: mscgh-poc/devsecops-templates/.github/workflows/cd.yml@main
    with:
      node-version: '22'
      environment: dev
      acr-name: ${{ vars.ACR_NAME }}
      image-name: eneisoft-demo
      resource-group: ${{ vars.RESOURCE_GROUP }}
      aks-name: ${{ vars.AKS_NAME }}
      k8s-namespace: eneisoft-demo
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
      AZURE_SUBSCRIPTION_ID: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```
