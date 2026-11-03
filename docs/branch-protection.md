# Configuración de Branch Protection y Teams

## Teams requeridos en la org `mscgh-poc`

### 1. `developers`
- **Permisos**: Write sobre `mic-eneisoft-demo`
- **Puede**: Crear feature branches, abrir PRs a develop
- **No puede**: Hacer push directo a develop ni a main

### 2. `approvers`
- **Permisos**: Maintain sobre `mic-eneisoft-demo`
- **Puede**: Aprobar PRs de develop a main
- **Responsabilidad**: Revisar que la integración en develop es estable antes de promover a producción

## Branch Protection Rules

### Rama `develop`
1. Ir a Settings > Branches > Add branch protection rule
2. Branch name pattern: `develop`
3. Activar:
   - [x] Require a pull request before merging
     - Required approvals: 1
   - [x] Require status checks to pass before merging
     - Status checks required:
       - `Tests`
       - `CodeQL Analysis`
   - [x] Require conversation resolution before merging
   - [x] Do not allow bypassing the above settings

### Rama `main`
1. Branch name pattern: `main`
2. Activar:
   - [x] Require a pull request before merging
     - Required approvals: 1
     - Require review from Code Owners: true
   - [x] Require status checks to pass before merging
     - Status checks required:
       - `Tests`
       - `CodeQL Analysis`
   - [x] Restrict who can dismiss pull request reviews
     - Teams: `approvers`
   - [x] Do not allow bypassing the above settings

### CODEOWNERS

Crear el archivo `.github/CODEOWNERS` en `mic-eneisoft-demo`:

```
# El team approvers debe aprobar cualquier PR a main
* @mscgh-poc/approvers
```

## GitHub Environments

### Environment: `dev`
- No requiere aprobación manual
- Rama restringida: `develop`

### Environment: `prod`
- Requiere aprobación de al menos 1 miembro de `approvers`
- Rama restringida: `main`
- Reviewers: team `approvers`
