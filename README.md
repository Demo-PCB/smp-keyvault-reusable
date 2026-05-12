# dso-keyvault-reusable

Workflows reutilizables para validar, empaquetar, publicar en JFrog y desplegar nombres de secretos en Azure Key Vault.

## Estrategia

- CI valida `keyvault/data.json` y `keyvault/config.yml`, genera un ZIP versionado y lo publica en JFrog.
- CD DEV descarga una versión desde JFrog y crea secretos faltantes en el Key Vault DEV.
- CD UAT descarga una versión desde JFrog y requiere aprobación mediante GitHub Environment `uat`.
- CD PRD descarga una versión desde JFrog y requiere aprobación mediante GitHub Environment `prd`.
- Rollback descarga una versión anterior desde JFrog y aplica la misma política segura.
- Rollback PRD usa GitHub Environment `prd-rollback`.

## Política de actualización Key Vault

El workflow no sobrescribe valores reales.

- Secreto faltante: se crea con valor inicial `DEMO`.
- Secreto existente con valor `DEMO`: no se modifica.
- Secreto existente con otro valor: se omite y no se crea nueva versión.

Rollback usa un artefacto anterior para asegurar que los nombres de secretos de esa versión existan. No elimina secretos adicionales ni restaura valores reales, porque los valores reales son gestionados manualmente por Seguridad en Azure Key Vault.

## Secrets/variables esperadas

Secrets:

- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_SUBSCRIPTION_ID`
- `DSO_ARTIFACTORY_TOKEN`

Variables:

- `IBK_ARTIFACTORY_URL`
