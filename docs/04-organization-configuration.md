# 04 - Configuración actual de la organización

En este capítulo dejo la **foto real** de la organización que tengo montada ahora mismo:

- Cuentas y OUs que existen.
- SCP aplicadas.
- Configuración básica de IAM Identity Center.

El diseño objetivo está en `02-architecture.md` y el paso a paso de algunos de los recursos en `03-implementation.md`.

---

## 4.1 Cuentas y OUs

Estructura actual:

- **Management Account**
  - `AWS-GENERAL-JVELAZQUEZ` → billing, configuración global, gobernanza.

- **OU Security**
  - *(vacía por ahora)* → futuras cuentas `log-archive` y `audit`.

- **OU Infrastructure**
  - *(vacía por ahora)* → futuros servicios compartidos (bastion, ECR, etc.).

- **OU Workloads**
  - **Prod** → `AWS-PROD-JVELAZQUEZ`
  - **Dev** → `AWS-DEV-JVELAZQUEZ`

- **OU Sandbox**
  - `PERSONAL-SANDBOX-JVELAZQUEZ` → sandbox personal.

Resumen:

| OU / Nivel            | Cuenta                        | Uso principal            |
|-----------------------|------------------------------|--------------------------|
| Root                  | `AWS-GENERAL-JVELAZQUEZ`      | Management / billing     |
| Security              | *(sin cuentas aún)*           | Logs / auditoría (plan)  |
| Infrastructure        | *(sin cuentas aún)*           | Servicios compartidos    |
| Workloads / Prod      | `AWS-PROD-JVELAZQUEZ`         | Producción               |
| Workloads / Dev       | `AWS-DEV-JVELAZQUEZ`          | Desarrollo / pruebas     |
| Sandbox               | `PERSONAL-SANDBOX-JVELAZQUEZ` | Sandbox personal         |

---

## 4.2 SCP en uso

Código completo en: `docs/scp/*.json`.

SCP definidas:

- **`SCP-Restrict-Regions`** → solo permite trabajar en regiones aprobadas.  
- **`SCP-Restrict-Root`** → limita el uso del usuario root.  
- **`SCP-Protect-CloudTrail`** → pensada para proteger CloudTrail en las futuras cuentas de Security.  
- **`SCP-Limit-Sandbox-Costs`** → restringe recursos caros en Sandbox.

> `IAMDeny` (de `03-implementation.md`) se usa solo como ejemplo de laboratorio.

Mapa por OU:

| OU / Cuenta                 | Restrict-Regions | Restrict-Root | Protect-CloudTrail          | Limit-Sandbox-Costs |
|----------------------------|------------------|---------------|-----------------------------|---------------------|
| Root                       | ✅ (global)       | ✅ (global)    | ❌                          | ❌                  |
| Security (OU)              | ✅               | ✅            | ✅ (futuras `log/audit`)    | ❌                  |
| Infrastructure (OU)        | ✅               | ✅            | ❌                          | ❌                  |
| Workloads / Prod (OU)      | ✅               | ✅            | ❌ (logs en Security)       | ❌                  |
| Workloads / Dev (OU)       | ✅               | ✅            | ❌                          | ❌                  |
| Sandbox / PERSONAL-SANDBOX | ✅               | ✅            | ❌                          | ✅                  |

---

## 4.3 IAM Identity Center

Uso **IAM Identity Center** como punto central de acceso:

- **Instancia**
  - Región: `us-east-1`.
  - Fuente de identidad: *Directorio de Identity Center*.
  - Portal: `https://josevelazquez.awsapps.com/start`.

- **Permission set**
  - `AdministratorAccess` (predefinido, 4h de sesión).

- **Identidades**
  - Grupo: `Administradores`.
  - Usuario ejemplo: `Alicia` → miembro de `Administradores`.

- **Asignaciones**
  - Grupo `Administradores` + `AdministratorAccess` en:
    - `AWS-DEV-JVELAZQUEZ`
    - `AWS-PROD-JVELAZQUEZ`

Resultado: cualquier usuario del grupo `Administradores` entra por el portal SSO,
elige DEV o PROD y accede con permisos administrativos.

![Portal de acceso mostrando las cuentas DEV y PROD con AdministratorAccess](./screenshots/03-identity-center-20.png)

---

## 4.4 Resumen

Con la configuración actual tengo:

- Entornos **Prod / Dev / Sandbox** separados en cuentas y OUs.  
- Reglas base de organización con SCP (regiones, root, sandbox).  
- Identity Center como **único punto de entrada** para administradores.

Es una base sencilla pero coherente sobre la que puedo seguir construyendo:
nuevas cuentas, más SCP, monitorización y automatización con IaC.
