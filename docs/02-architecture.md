# 02 - Arquitectura

En este documento dejo por escrito **cómo está pensada la organización de AWS** a nivel
de cuentas y OUs, tomando como referencia una landing zone sencilla.

Primero enseño el diagrama y después resumo la idea general, los principios de diseño
y qué parte es **real** y qué parte es **objetivo futuro**.

---

## 1. Diagrama de la organización

![Organization](./screenshots/02-aws_organization_architecture.png)

El diagrama muestra la **arquitectura objetivo**: cómo quiero que quede la organización
cuando esté completa, no solo el estado actual del laboratorio.

---

## 2. Idea general

El diseño sigue una estructura clara por función:

- **Management Account**
  - `AWS-GENERAL-JVELAZQUEZ`  
  Cuenta dedicada a facturación y gobernanza central.

- **OU Security**
  - `log-archive-account` *(objetivo)*  
  - `audit-account` *(objetivo)*  
  Pensadas para centralizar logs y auditoría.

- **OU Infrastructure**
  - `shared-services-account` *(objetivo)*  
  Servicios compartidos para varias aplicaciones (por ejemplo, ECR, bastion, tooling).

- **OU Workloads**
  - **Prod OU**
    - `AWS-PROD-JVELAZQUEZ` → entorno de producción.
  - **Dev OU**
    - `AWS-DEV-JVELAZQUEZ` → entorno de desarrollo / no producción.

- **OU Sandbox**
  - `PERSONAL-SANDBOX-JVELAZQUEZ` → entorno de pruebas personales.

A día de hoy, en el laboratorio solo existen realmente estas cuentas:

- `AWS-GENERAL-JVELAZQUEZ`  
- `AWS-PROD-JVELAZQUEZ`  
- `AWS-DEV-JVELAZQUEZ`  
- `PERSONAL-SANDBOX-JVELAZQUEZ`  

El resto son **cuentas planificadas** para un escenario más completo.

---

## 3. Principios de diseño

### 3.1 Separar control y carga de trabajo

- La Management Account no ejecuta aplicaciones:  
  se usa para facturación, OUs, SCP y Identity Center.
- Las cargas de trabajo viven en cuentas de **Workloads** (Prod/Dev).
- Seguridad y servicios compartidos van en OUs dedicadas (`Security`, `Infrastructure`).

Así la capa de control no se mezcla con los entornos de negocio.

### 3.2 Limitar el impacto de errores

- Producción está aislada de Desarrollo y Sandbox.  
- Un fallo en Dev o en Sandbox (pruebas, borrados, depuraciones) se queda en esa cuenta.  
- Sandbox tiene su propia OU para poder aplicar reglas específicas de coste y riesgo.

### 3.3 Gobernanza por bloques (OUs)

La estructura de OUs está pensada para que las **SCP** se apliquen “por bloque”:

- `Security` → políticas más estrictas sobre cuentas críticas de logging/auditoría.  
- `Infrastructure` → controles específicos sobre servicios comunes.  
- `Workloads/Prod` vs `Workloads/Dev` → posibilidad de endurecer más producción.  
- `Sandbox` → foco en limitar recursos caros y experimentos.

El detalle de las SCP concretas está en `04-current-configuration.md`.

### 3.4 Pensando en crecer

Aunque ahora solo hay unas pocas cuentas, el esquema admite:

- Nuevas cuentas de workloads (por app, por equipo, por región).  
- Más cuentas de seguridad (por ejemplo, incident response).  
- Más servicios compartidos en `Infrastructure`.

La idea es que la organización no haya que rediseñarla cada vez que aparezca un
nuevo proyecto.

---

## 4. Foto actual vs diseño objetivo

Para resumir:

**Desplegado hoy en el laboratorio**

- Cuentas:
  - `AWS-GENERAL-JVELAZQUEZ` (Management).
  - `AWS-PROD-JVELAZQUEZ` (Prod).
  - `AWS-DEV-JVELAZQUEZ` (Dev).
  - `PERSONAL-SANDBOX-JVELAZQUEZ` (Sandbox personal).
- OUs:
  - `Security` (vacía por ahora).
  - `Infrastructure` (vacía por ahora).
  - `Workloads/Prod` → `AWS-PROD-JVELAZQUEZ`.
  - `Workloads/Dev` → `AWS-DEV-JVELAZQUEZ`.
  - `Sandbox` → `PERSONAL-SANDBOX-JVELAZQUEZ`.

**Arquitectura objetivo (planificada)**

- `log-archive-account` y `audit-account` bajo `Security`.  
- `shared-services-account` bajo `Infrastructure`.  
- Posibles cuentas adicionales de workloads siguiendo el mismo patrón.

Esta arquitectura es la base sobre la que luego aplico:

- Las SCP descritas en `04-current-configuration.md`.  
- El modelo de acceso centralizado con IAM Identity Center que detallo en `03-implementation.md`.
