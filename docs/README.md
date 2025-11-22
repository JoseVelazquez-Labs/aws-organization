# AWS Multi-Account Landing Zone (Lab)

Laboratorio personal en AWS enfocado en **gobernanza multi-cuenta** y acceso centralizado, pensado como parte de mi portfolio.

El objetivo es simular cómo una empresa podría organizar sus cuentas en AWS, separar entornos (Prod/Dev/Sandbox) y aplicar controles básicos de seguridad y costes.

---

## 🚀 Qué demuestra este proyecto

- Diseño de una **landing zone multi-account** usando AWS Organizations.
- Separación de entornos en cuentas y OUs: **Security, Infrastructure, Workloads, Sandbox**.
- Uso de **Service Control Policies (SCP)** para:
  - Restringir regiones.
  - Limitar el uso del usuario root.
  - Proteger CloudTrail (diseño futuro).
  - Controlar costes en Sandbox.
- Configuración de **IAM Identity Center**:
  - Usuarios y grupos.
  - Conjuntos de permisos (`AdministratorAccess` para laboratorio).
  - Asignación de accesos a varias cuentas desde un portal central.

---

## 🧱 Arquitectura (visión rápida)

> Diagrama de la organización (Root, Management Account, OUs y cuentas).

![Diagrama de la organización](./docs/screenshots/02-org-diagram.png)

> El diseño completo está descrito en `docs/02-architecture.md`.

---

## 📂 Estructura del repositorio

```text
docs/
  01-overview.md        # Resumen del laboratorio y objetivos
  02-architecture.md    # Diseño objetivo: cuentas, OUs, diagrama y principios
  03-implementation.md  # How-to paso a paso (Organizations, SCP, Identity Center)
  04-configuration.md   # Estado real actual y mapa de SCP por OU/cuenta
  05-lessons-learned.md # Aprendizajes y posibles mejoras (Control Tower, IaC, etc.)
  scp/
    README.md           # Explicación de las SCP
    SCP-Restrict-Regions.json
    SCP-Restrict-Root.json
    SCP-Protect-CloudTrail.json
    SCP-Limit-Sandbox-Costs.json
  screenshots/          # Capturas de la consola AWS usadas en la doc

