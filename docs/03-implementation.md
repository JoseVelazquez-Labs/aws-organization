# 03 - Implementation

## 1. Prerrequisitos

- Cuenta de AWS con permisos de `OrganizationAccountAccessRole` o equivalente.
- Billing correctamente configurado.
- (Opcional) Terraform instalado (`>= 1.6`) si se utiliza IaC.

## 2. Creación de la AWS Organization

1. Desde la cuenta raíz, ir a **AWS Organizations**.
2. Seleccionar **Create an organization**.
3. Elegir modo `Consolidated Billing` (por defecto) y confirmar.

![Pantalla de creación de la Organization](./screenshots/03-create-organization.png)

> A partir de este momento, la cuenta actual actúa como **Management Account**.
