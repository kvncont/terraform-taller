# Taller de Terraform
El siguiente taller tiene como objectivo comprender la forma en que funciona la herramienta de terraform.

## Agenda
* Intro a terraform (Cloud Agnostico)
* Proveedores (Azure, AWS, GCP, etc)
* Autenticación (Azure)](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
* Crear un recurso
* Modificar de un recurso
* Eliminar recurso

### Paso 1 (Autenticación):
```bash
# Azure login
az login

# Muestra las suscripciones disponibles para la cuenta
az account list -o table

# Establece la suscripcion predeterminada
az account set -s <subscription_id>
```

### Paso 2 ():