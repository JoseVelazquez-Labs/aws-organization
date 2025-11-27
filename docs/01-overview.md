# 01 - Overview

Este laboratorio nace con una idea sencilla: **practicar cómo organizar varias cuentas
de AWS como si estuviera en una empresa real**, y dejarlo documentado para poder
mostrarlo en mi portfolio.

El foco no está en desplegar aplicaciones complejas, sino en la parte de
**gobernanza, acceso y estructura de cuentas**: AWS Organizations, OUs, SCP e
IAM Identity Center.

No es una landing zone enterprise completa, pero sí un primer diseño razonable
sobre el que se podría construir algo más avanzado.

---

## 1. Qué quiero conseguir con este laboratorio

A nivel práctico, este proyecto me sirve para:

- Diseñar una **organización multi-cuenta sencilla** (Management + Prod + Dev + Sandbox).
- Agrupar cuentas en OUs orientadas a:
  - `Security`
  - `Infrastructure`
  - `Workloads`
  - `Sandbox`
- Aplicar **Service Control Policies (SCP)** para:
  - Restringir regiones.
  - Limitar el uso del usuario root.
  - Preparar la protección de servicios clave (como CloudTrail).
  - Poner límites de coste en la cuenta de Sandbox.
- Centralizar el acceso con **IAM Identity Center**, utilizando:
  - Usuarios y grupos.
  - Conjuntos de permisos (por ejemplo, `AdministratorAccess` para el laboratorio).

La idea es tratarlo como un **mini caso real**: qué estructura tendría sentido, qué
controles pondría primero y cómo lo documentaría para otros.

---

## 2. Qué servicios toco realmente

Los servicios principales que aparecen en el laboratorio son:

- **AWS Organizations**: creación de la organización, OUs, cuentas miembro y asociación de SCP.
- **IAM**: roles entre cuentas (`OrganizationAccountAccessRole`) y concepto de permisos.
- **IAM Identity Center**: SSO multi-cuenta con grupos, permission sets y portal de acceso.
- **CloudTrail**: punto de partida para el logging centralizado y objetivo de futuras SCP
  de protección.

Otros servicios pueden aparecer de forma puntual, pero el foco de este proyecto es
la **capa de organización y acceso**, no las cargas de trabajo.

---

## 3. Alcance y límites

Este laboratorio cubre:

- La estructura de la organización (OUs y cuentas).
- La aplicación de un conjunto inicial de SCP.
- Un modelo muy básico de acceso con Identity Center para administradores.

No cubre (de momento):

- Automatización completa con Infrastructure as Code.
- Control Tower ni soluciones gestionadas de landing zone.
- Monitorización y cumplimiento avanzados (Config, Security Hub, etc.).

Estos puntos se mencionan como líneas de mejora en `05-lessons-learned.md`.

---

## 4. Consideraciones de coste

Aunque la idea es mantener el laboratorio con un coste mínimo, hay dos aspectos que
considero importantes tras mi propia experiencia:

- **Budgets**  
  Antes de crear la organización, es muy recomendable configurar uno o varios
  **AWS Budgets** con alertas por correo (por ejemplo, al 50 %, 80 % y 100 % del
  presupuesto estimado). Es una forma sencilla de detectar cualquier gasto inesperado.

- **Créditos promocionales y free tier**  
  Algunas cuentas nuevas incluyen créditos (por ejemplo, 200 USD) o ventajas de free tier.
  En mi caso, al utilizar esa cuenta como **Management Account** dentro de una AWS
  Organization, esos créditos dejaron de aplicarse.  
  Por prudencia, es buena idea revisar las condiciones de los créditos y valorar si
  conviene usar otra cuenta distinta como Management Account antes de crear la organización.

---

## 5. Relación con el resto de documentos

Este archivo solo da el contexto general.  
Para ver el detalle técnico:

- `02-architecture.md` explica cómo está pensada la organización (diagrama y principios).
- `03-implementation.md` recoge los pasos concretos que he seguido.
- `04-current-configuration.md` muestra la foto real de cómo está montado ahora mismo.
- `05-lessons-learned.md` resume los aprendizajes y posibles mejoras.
