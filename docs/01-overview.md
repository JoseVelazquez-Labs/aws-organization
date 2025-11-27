# 01 - Overview

Este laboratorio tiene un objetivo simple: **practicar cómo organizar varias cuentas
de AWS con cabeza**, como lo haría en una empresa, y dejar ese trabajo documentado
para poder enseñarlo en mi portfolio.

El foco está en la **capa de organización**, no en las aplicaciones:

- Estructura de cuentas y OUs con **AWS Organizations**.  
- Primeras reglas de **gobernanza y seguridad** con **SCP**.  
- Acceso centralizado con **IAM Identity Center**.

No es una landing zone enterprise, pero sí un diseño realista sobre el que se podría
construir algo más completo.

---

## 1. En qué consiste el laboratorio

En lugar de montar recursos sueltos, trabajo sobre una **organización multi-cuenta**
con esta idea:

- Una cuenta de administración (*Management Account*).  
- Cuentas separadas para **Producción**, **Desarrollo** y **Sandbox personal**.  
- OUs pensadas para crecer: `Security`, `Infrastructure`, `Workloads`, `Sandbox`.  
- Un conjunto inicial de **SCP** para:
  - Restringir regiones.
  - Limitar el uso del usuario root.
  - Proteger servicios clave (como CloudTrail, a futuro).
  - Reducir riesgos de coste en Sandbox.
- Un modelo sencillo de acceso con **IAM Identity Center**:
  - Usuarios y grupos.
  - Permission sets reutilizables entre cuentas.

La idea es tratarlo como un **mini caso real** y no como un ejemplo aislado.

---

## 2. Alcance real del proyecto

Este laboratorio **sí cubre**:

- La estructura de la organización (cuentas + OUs).  
- Un conjunto inicial de SCP aplicado sobre Root y algunas OUs.  
- Un modelo básico de acceso administrativo con IAM Identity Center.

Y **no cubre todavía**:

- Automatización con IaC (Terraform / CloudFormation).  
- Control Tower ni soluciones gestionadas de landing zone.  
- Monitorización y cumplimiento avanzados (Config, Security Hub, etc.).

Estos puntos aparecen como posibles mejoras en `05-lessons-learned.md`.

---

## 3. Costes y lecciones rápidas

Aunque el montaje es de laboratorio, hay dos temas de coste que he tenido en cuenta:

- **AWS Budgets**  
  Es recomendable crear uno o varios presupuestos con alertas (50 %, 80 %, 100 %)
  antes de montar la organización, para detectar cualquier gasto inesperado.

- **Créditos y free tier**  
  Algunas cuentas incluyen créditos promocionales (por ejemplo, 200 USD).  
  En mi caso, al usar esa cuenta como **Management Account** dentro de una
  AWS Organization, esos créditos dejaron de aplicarse.  
  Merece la pena revisar las condiciones y valorar si la Management Account
  debería ser otra cuenta distinta.

---

## 4. Cómo leer el resto de documentos

- `02-architecture.md` → cómo está pensada la organización (diagrama y principios).  
- `03-implementation.md` → pasos que he seguido para montarla.  
- `04-current-configuration.md` → foto real de lo que existe hoy.  
- `05-lessons-learned.md` → qué he aprendido y cómo lo mejoraría.

