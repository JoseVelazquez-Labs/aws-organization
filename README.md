# AWS Multi-account Organization Lab

Este repositorio recoge un laboratorio personal donde diseño e implemento
una **organización multi-cuenta en AWS** utilizando:

- AWS Organizations  
- IAM + Service Control Policies (SCP)  
- IAM Identity Center (SSO)

No es todavía una landing zone corporativa completa, pero sí una **base de organización**
sobre la que podría evolucionar algo más avanzado en el futuro.

---

## Objetivo del proyecto

- Practicar la creación de una **AWS Organization** real desde cero.  
- Separar entornos (Producción / Desarrollo / Sandbox) en cuentas distintas.  
- Aplicar una primera capa de **gobernanza, seguridad y control de costes**.  
- Documentar todo el proceso como si fuera un pequeño proyecto interno de empresa.

El proyecto está pensado como pieza de portfolio para un perfil **Cloud / DevOps junior**
en transición hacia roles más centrados en AWS.

---

## Estructura del repositorio

```text
aws-organization/
├── docs/
│   ├── 01-overview.md              # Contexto, alcance y consideraciones de coste
│   ├── 02-architecture.md          # Diseño objetivo de la organización (diagrama + explicación)
│   ├── 03-implementation.md        # How-to: pasos para crear la organización y los recursos clave
│   ├── 04-current-configuration.md # Estado real del laboratorio (qué está desplegado de verdad)
│   └── 05-lessons-learned.md       # Principales aprendizajes y mejoras futuras
│
├── docs/scp/                       # Service Control Policies en formato JSON
│   ├── SCP-Restrict-Regions.json
│   ├── SCP-Restrict-Root.json
│   ├── SCP-Protect-CloudTrail.json
│   └── SCP-Limit-Sandbox-Costs.json
│
└── docs/screenshots/               # Capturas de consola usadas en la documentación


```

Lectura recomendada:

1. `01-overview.md`  
2. `02-architecture.md`  
3. `03-implementation.md`  
4. `04-current-configuration.md`  
5. `05-lessons-learned.md`

---

## Consideraciones de coste

Aunque la idea es mantener el laboratorio lo más barato posible, hay dos puntos
que conviene tener en cuenta:

- **Budgets**  
  Antes de crear la organización es muy recomendable configurar uno o varios
  **AWS Budgets** con alertas por correo (por ejemplo, al 50 %, 80 % y 100 %
  del importe que quieras gastar). Es una forma sencilla de no llevarse sustos.

- **Créditos promocionales y free tier**  
  Algunas cuentas nuevas incluyen créditos (por ejemplo, 200 USD) o ventajas de
  free tier. En mi caso, al usar esa cuenta como **Management Account** dentro
  de una AWS Organization, los créditos dejaron de aplicarse.  
  Por seguridad, merece la pena revisar las condiciones de los créditos y valorar
  si usar otra cuenta distinta como Management Account.

---

## Estado actual

A día de hoy, la organización cuenta con:

- Cuentas separadas para **Producción**, **Desarrollo** y **Sandbox personal**.  
- OUs definidas para **Security**, **Infrastructure**, **Workloads** y **Sandbox**.  
- Un primer conjunto de **SCP** aplicadas sobre Root y algunas OUs.  
- **IAM Identity Center** configurado con:
  - Instancia asociada a la organización.  
  - Grupo de administradores.  
  - Conjunto de permisos `AdministratorAccess` asignado a varias cuentas.

El detalle completo del estado actual está descrito en  
`04-current-configuration.md`.

---

## Próximos pasos

Algunas líneas de trabajo futuras (también comentadas en `05-lessons-learned.md`):

- Valorar el uso de **AWS Control Tower** para estandarizar el despliegue.  
- Añadir servicios de **monitorización y cumplimiento** (CloudTrail centralizado,
  AWS Config, Security Hub, etc.).  
- Automatizar la creación de cuentas, OUs e SCP con **Infrastructure as Code**
  (Terraform o CloudFormation).
