# **Taller de Terraform**
El siguiente taller tiene como objectivo comprender la forma en que funciona la herramienta de terraform con un simple ejercicio donde se creara, modificara y eliminara un recurso en azure.

Requisitos:
* Docker (Se requiere para ejecutar el devcontainer que tiene todo el ambiente necesario para el taller)


## **Agenda**
* Intro a terraform (Cloud Agnostico)
* [Proveedores (Azure, AWS, GCP, etc)](https://registry.terraform.io/browse/providers)
* [Autenticación (Azure)](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
* [Crear un recurso](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/container_group)
* Modificar de un recurso
* Eliminar recurso

### **Paso 1 (Autenticación):**
```bash
# Azure login
az login

# Muestra las suscripciones disponibles para la cuenta
az account list -o table

# Establece la suscripcion predeterminada
az account set -s <subscription_id>
```

### **Paso 2 (Pegar el siguiente contenido en el main.tf):**
```
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "3.42.0"
    }
  }
}

provider "azurerm" {
  features {}
}

variable "user_name" {
  type        = string
  description = "Nombre de usuario"
  default     = ""
}

data "azurerm_resource_group" "example" {
  name     = "example-resources"
}

resource "azurerm_container_group" "example" {
  name                = "aci-${var.user_name}"
  location            = data.azurerm_resource_group.example.location
  resource_group_name = data.azurerm_resource_group.example.name
  ip_address_type     = "Public"
  dns_name_label      = "aci-${var.user_name}"
  os_type             = "Linux"

  container {
    name   = "hello-world"
    image  = "mcr.microsoft.com/azuredocs/aci-helloworld:latest"
    cpu    = "0.5"
    memory = "1"

    ports {
      port     = 80
      protocol = "TCP"
    }
  }

  tags = {
    environment = "sandbox"
    # owner       = var.user_name
  }
}

output "aci_fqdn" {
  value = azurerm_container_group.example.fqdn
}
```

### **Paso 3 (Ejecutar el plan):**
```bash
cd terraform
terraform init
terraform validate
terraform plan
```

### **Paso 4 (Aplicar el plan):**
```bash
terraform apply
```

### **Paso 5 (Revisar implementacion):**
Para revisar si la implementacion esta de acuerdo a lo deseado se puede ingresar al [portal de azure](https://portal.azure.com/#home) para revisar la configuracion del recurso aprovisionado o bien se puede hacer consultas mediante la linea de comandos de azure como se muestra abajo:
```bash
# Lista todos los container groups dentro del grupo de recursos especificado
az container list --resource-group example-resources -o table

# Muestra la informacion del container group especificado
az container show --name my-container --resource-group example-resources -o table
```

### **Paso 6 (Actualizar el recurso):**
```bash
terraform validate
terraform plan
terraform apply
```

### **Paso 7 (Destruir el recurso):**
```bash
terraform destroy
```