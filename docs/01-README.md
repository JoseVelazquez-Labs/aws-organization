# 01 - Overview

Este repositorio recoge un **laboratorio personal de AWS Organizations** orientado a:
- Practicar **gobernanza multi-cuenta**.
- Documentar el proceso de forma clara para mi **portfolio**.

No es una landing zone enterprise, pero sí un diseño realista que se podría ampliar en una empresa de verdad.

---

## 1. Objetivo del laboratorio

- Definir una **landing zone multi-cuenta sencilla**.
- Separar entornos: **Prod, Dev, Sandbox**.
- Aplicar **SCP básicas** (regiones, root, costes, CloudTrail).
- Centralizar acceso con **IAM Identity Center**.
- Dejar todo documentado como si fuera un pequeño caso real.

---

## 2. Servicios usados

- **AWS Organizations**: OUs, cuentas miembro y SCP.
- **IAM**: roles entre cuentas (`OrganizationAccountAccessRole`).
- **IAM Identity Center**: usuarios, grupos y permission sets (por ejemplo, `AdministratorAccess`).
- **CloudTrail**: servicio a proteger con SCP en las cuentas de seguridad.

---

## 3. Estructura del repo

```text
aws-organization/
└── docs/
    ├── 01-overview.md        # Visión general (este archivo)
    ├── 02-architecture.md    # Diseño objetivo (diagrama y principios)
    ├── 03-implementation.md  # How-to paso a paso
    ├── 04-configuration.md   # Estado real actual y SCP aplicadas
    ├── 05-lessons-learned.md # Aprendizajes y mejoras futuras
    └── scp/
        ├── README.md
        ├── SCP-Restrict-Regions.json
        ├── SCP-Restrict-Root.json
        ├── SCP-Protect-CloudTrail.json
        └── SCP-Limit-Sandbox-Costs.json
