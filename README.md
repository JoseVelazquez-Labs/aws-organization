# AWS Organization (Lab)

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

![Diagrama de la organización](./screenshots/02-aws_organization_architecture.png)

> El diseño completo está descrito en `docs/02-architecture.md`.

---

## 📖 Por dónde empezar

Si quieres revisar el proyecto en detalle, recomiendo este orden:

docs/01-overview.md → contexto y objetivos.

docs/02-architecture.md → diagrama y diseño de la organización.

docs/03-implementation.md → implementación paso a paso.

docs/04-configuration.md → foto real de cómo está ahora el laboratorio.

docs/05-lessons-learned.md → reflexión y siguientes pasos.

