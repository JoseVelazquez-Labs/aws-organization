# 01 - Overview

Este repositorio recoge un **laboratorio personal de AWS Organizations** orientado a:
- Practicar **gobernanza multi-cuenta** en AWS.
- Separar entornos (Prod/Dev/Sandbox) de forma ordenada.
- Dejar una documentación clara que pueda enseñar en mi **portfolio**.

No pretende ser una landing zone enterprise completa, sino una base realista sobre la que se podría construir algo más grande.

---

## 1. Objetivo del laboratorio

- Diseñar una **landing zone multi-account sencilla**.
- Organizar cuentas en OUs: **Security, Infrastructure, Workloads, Sandbox**.
- Aplicar **Service Control Policies (SCP)** para:
  - Restringir regiones.
  - Limitar el uso del usuario root.
  - Proteger servicios clave (CloudTrail, cuentas de seguridad).
  - Controlar costes en Sandbox.
- Centralizar accesos con **IAM Identity Center** (usuarios, grupos, permission sets).
- Documentar todo como si fuese un pequeño caso real de empresa.

---

## 2. Servicios principales utilizados

- **AWS Organizations** → OUs, cuentas miembro, SCP.
- **IAM** → roles entre cuentas (`OrganizationAccountAccessRole`).
- **IAM Identity Center** → SSO multi-cuenta con grupos y permission sets.

---

## 3. Estructura del repositorio

```text
docs/
  01-overview.md        # Visión general y objetivos (este archivo)
  02-architecture.md    # Diseño objetivo: cuentas, OUs, diagrama y principios
  03-implementation.md  # How-to paso a paso (Organizations, SCP, Identity Center)
  04-configuration.md   # Estado real actual y mapa de SCP por OU/cuenta
  05-lessons-learned.md # Aprendizajes y mejoras futuras
  scp/
    README.md           # Explicación de las SCP
    SCP-Restrict-Regions.json
    SCP-Restrict-Root.json
    SCP-Protect-CloudTrail.json
    SCP-Limit-Sandbox-Costs.json
