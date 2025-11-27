# AWS Multi-account Organization Lab

Laboratorio personal donde diseño e implemento una **organización multi-cuenta en AWS**
utilizando:

- AWS Organizations  
- IAM + Service Control Policies (SCP)  
- IAM Identity Center (SSO)

No es una landing zone corporativa completa, sino una **base de organización** pensada
para aprender gobernanza en AWS y usarla como proyecto de portfolio.

---

## Objetivo

- Crear una **AWS Organization** real desde cero.  
- Separar entornos (Producción / Desarrollo / Sandbox) en cuentas distintas.  
- Aplicar una primera capa de **gobernanza, seguridad y control de costes**.  
- Gestionar el acceso de forma centralizada con **IAM Identity Center**.  

---

## Diagrama de la organización

![Diagrama de la organización AWS](./docs/screenshots/02-aws_organization_architecture.png)

> Detalle completo del diseño en `docs/02-architecture.md`.

---

## Estructura del repositorio

```text
aws-organization/
├── docs/
│   ├── 01-overview.md
│   ├── 02-architecture.md
│   ├── 03-implementation.md
│   ├── 04-current-configuration.md
│   └── 05-lessons-learned.md
│
├── docs/scp/
│   ├── SCP-Restrict-Regions.json
│   ├── SCP-Restrict-Root.json
│   ├── SCP-Protect-CloudTrail.json
│   └── SCP-Limit-Sandbox-Costs.json
│
└── docs/screenshots/

````

Lectura recomendada:

1. `01-overview.md` → contexto y alcance  
2. `02-architecture.md` → diseño objetivo  
3. `03-implementation.md` → how-to paso a paso  
4. `04-current-configuration.md` → estado real actual  
5. `05-lessons-learned.md` → aprendizajes y mejoras

---

## Costes

- **AWS Budgets**: recomendable configurar alertas (50 %, 80 %, 100 %) antes de crear
  la organización.  
- **Créditos / free tier**: al usar mi cuenta con créditos como **Management Account**
  dentro de una AWS Organization, los créditos dejaron de aplicarse. Conviene revisar
  las condiciones y valorar usar otra cuenta para la Management Account.

---

## Estado actual (resumen)

- Cuentas separadas para **Prod**, **Dev** y **Sandbox personal**.  
- OUs creadas: **Security**, **Infrastructure**, **Workloads**, **Sandbox**.  
- Conjunto inicial de **SCP** aplicado a Root y OUs principales.  
- **IAM Identity Center** configurado con grupo de administradores y
  `AdministratorAccess` en varias cuentas.

