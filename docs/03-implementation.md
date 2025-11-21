# 03 - Implementation (How-to)

En este apartado **no** describo todavía la arquitectura final de la organización, sino
un **how-to práctico** donde voy construyendo, paso a paso, los recursos básicos que
usaré después:

- AWS Organization.
- Cuentas miembro (producción y desarrollo).
- Unidades Organizativas (OU).
- Una Service Control Policy (SCP) de ejemplo.
- Configuración inicial de IAM Identity Center.

La arquitectura completa (todas las cuentas, OUs y políticas finales) se explica en el
capítulo **02 – Architecture**.

---

## Tabla de contenidos

- [1. Prerrequisitos](#1-prerrequisitos)
- [2. Creación de la AWS Organization](#2-creación-de-la-aws-organization)
- [3. Gestión de cuentas en la AWS Organization](#3-gestión-de-cuentas-en-la-aws-organization)
  - [3.1 Invitación de la cuenta de Producción](#31-invitación-de-la-cuenta-de-producción)
  - [3.2 Creación del rol de acceso entre cuentas](#32-creación-del-rol-de-acceso-entre-cuentas)
  - [3.3 Prueba de acceso desde la Management Account](#33-prueba-de-acceso-desde-la-management-account)
- [4. Aprovisionamiento de nuevas cuentas desde AWS Organizations](#4-aprovisionamiento-de-nuevas-cuentas-desde-aws-organizations)
  - [4.1 Creación de la cuenta de Desarrollo](#41-creación-de-la-cuenta-de-desarrollo-desde-la-organization)
  - [4.2 Acceso a la cuenta de Desarrollo](#42-acceso-a-la-cuenta-de-desarrollo-desde-la-management-account)
- [5. Unidades Organizativas (OU)](#5-unidades-organizativas-ou)
  - [5.1 OU de Producción](#51-creación-de-la-ou-de-producción)
  - [5.2 OU de Desarrollo](#52-creación-de-la-ou-de-desarrollo)
  - [5.3 Situación inicial y objetivo](#53-situación-inicial-de-las-cuentas-y-objetivo-de-la-reorganización)
  - [5.4 Trasladar las cuentas a su OU](#54-trasladar-las-cuentas-a-su-ou-correspondiente)
- [6. Service Control Policies (SCP)](#6-service-control-policies-scp--ejemplo-de-denegación-de-iam)
  - [6.1 Habilitar SCP en la organización](#61-habilitar-las-políticas-de-control-de-servicios)
  - [6.2 Creación de la política `IAMDeny`](#62-creación-de-la-política-iamdeny)
  - [6.3 Asociación a la cuenta de Producción](#63-asociación-de-la-scp-a-la-cuenta-de-producción)
  - [6.4 Verificación del efecto](#64-verificación-del-efecto-de-la-scp)
- [7. IAM Identity Center – Configuración inicial](#7-iam-identity-center--configuración-inicial)
  - [7.1 Habilitar la instancia](#71-habilitar-la-instancia-de-iam-identity-center)
  - [7.2 Fuente de identidad y portal](#72-configuración-de-la-fuente-de-identidad-y-del-portal-de-acceso)
  - [7.3 Conjunto de permisos `AdministratorAccess`](#73-creación-de-un-conjunto-de-permisos-predefinido-administratoraccess)
  - [7.4 Grupo `Administradores`](#74-creación-del-grupo-de-administradores)
  - [7.5 Usuario de ejemplo `Alicia`](#75-creación-del-usuario-alicia-y-asignación-al-grupo)
  - [7.6 Asignar el grupo a las cuentas](#76-asignar-el-grupo-administradores-a-las-cuentas-de-aws)
  - [7.7 Verificación de acceso desde el portal](#77-verificación-de-acceso-desde-el-portal-como-usuaria-alicia)

---

## 1. Prerrequisitos

Antes de empezar, necesito:

- Una cuenta de AWS que actuará como **Management Account**, con permisos
  administrativos sobre **AWS Organizations**.
- La facturación correctamente configurada en la Management Account.
- Acceso a la cuenta que se va a invitar como entorno de **Producción**, evitando
  usar el usuario root salvo para tareas imprescindibles.

---

## 2. Creación de la AWS Organization

Lo primero es crear la **AWS Organization** desde la cuenta que será la
**Management Account**.  
Esta cuenta se encargará de la gobernanza global, la facturación consolidada y la
gestión del resto de cuentas.

1. Con un usuario IAM con permisos de administración en la Management Account
   (no con el root), accedo al servicio **AWS Organizations**.
2. Selecciono **Crear una organización**.

![Pantalla de creación de la Organization](./screenshots/03-create-organization.png)

3. Una vez creada, aparece la estructura inicial de la organización, que se irá
   llenando a medida que añada nuevas cuentas.

![Organización creada con la Management Account](./screenshots/03-organization-created.png)

---

## 3. Gestión de cuentas en la AWS Organization

En este laboratorio, además de la Management Account, añado una cuenta que
representa el entorno de **Producción**.  
Para conseguirlo:

1. Invito una cuenta existente a la organización.
2. Configuro un rol para que la Management Account pueda administrarla.

> Más adelante, en la arquitectura final, se añadirán más cuentas y se explicará
> la estructura completa. Aquí me centro en **un flujo representativo**.

### 3.1 Invitación de la cuenta de Producción

1. Desde la Management Account, en **AWS Organizations**, selecciono  
   **Agregar una cuenta de AWS**.
2. Indico el **ID de la cuenta** a invitar o el **correo electrónico** asociado.  
   En este laboratorio he creado previamente una cuenta para producción.  
   A continuación, pulso **Enviar petición**.

![Invitar una cuenta a la organización](./screenshots/03-invite-account.png)

3. Accedo a la consola de AWS con la cuenta invitada. En **AWS Organizations**
   aparece la invitación pendiente:

![Invitación recibida en la cuenta invitada](./screenshots/03-account-invited.png)

4. Acepto la invitación desde la cuenta de Producción y vuelvo a la Management
   Account. Ahora la organización muestra ambas cuentas:

![Dos cuentas en la organización: Management y Producción](./screenshots/03-two-accounts.png)

### 3.2 Creación del rol de acceso entre cuentas

Para que la Management Account pueda administrar la cuenta de Producción sin
usar credenciales propias de esa cuenta, creo un **rol de IAM** en la cuenta de
Producción que la Management Account podrá asumir.

Este paso se realiza **desde la cuenta de Producción**:

1. En el servicio **IAM**, accedo a **Roles → Create role**.
2. Elijo **Cuenta de AWS** como tipo de entidad de confianza e indico el ID de la
   Management Account. De esta forma, solo esa cuenta podrá asumir el rol.

> Apunte: cuando una cuenta se crea directamente desde la organización, en lugar
> de invitar una existente, el rol `OrganizationAccountAccessRole` se genera
> automáticamente.

![Creación del rol confiando en la Management Account](./screenshots/03-iam-role.png)

3. Asigno la política de permisos **AdministratorAccess**, ya que en este
   laboratorio quiero que la Management Account tenga control administrativo
   completo sobre la cuenta de Producción. En un entorno real lo habitual sería
   restringirla a un conjunto más específico de permisos.
4. Creo el rol con el nombre `OrganizationAccountAccessRole`.

![Rol `OrganizationAccountAccessRole` con AdministratorAccess](./screenshots/03-iam-role-2.png)

### 3.3 Prueba de acceso desde la Management Account

Para confirmar que todo está bien configurado, pruebo a asumir el rol desde la
Management Account:

1. Desde la consola de la Management Account, utilizo el menú **Cambiar rol**.

![Selección de cuenta y rol](./screenshots/03-switch-pro-role.png)

2. Indico la cuenta de Producción y el rol `OrganizationAccountAccessRole`, y
   pulso **Cambiar función**.

![Confirmación de cambio de rol](./screenshots/03-switch-pro-role-2.png)

3. La consola pasa a mostrar que estoy operando dentro de la cuenta de Producción
   usando el rol compartido, sin haber utilizado credenciales directas de esa
   cuenta.

![Rol asumido correctamente en la cuenta de Producción](./screenshots/03-pro-role-switched.png)

---

## 4. Aprovisionamiento de nuevas cuentas desde AWS Organizations

En el apartado anterior he visto cómo **invitar una cuenta existente** y crear el
rol `OrganizationAccountAccessRole` manualmente.

Ahora utilizo un enfoque distinto: **crear una nueva cuenta directamente desde
AWS Organizations**. Esto simplifica parte del proceso y hace más fácil
estandarizar cómo se añaden nuevas cuentas.

En este ejemplo, la nueva cuenta representa el entorno de **Desarrollo**.

### 4.1 Creación de la cuenta de Desarrollo desde la Organization

1. Desde la **Management Account**, accedo a **AWS Organizations**.
2. En la sección de cuentas, selecciono **Crear una cuenta de AWS**.
3. Indico:
   - Nombre de la cuenta (por ejemplo, `Development`).
   - Correo electrónico asociado a la nueva cuenta.
   - (Opcional) Un rol de IAM que se creará automáticamente para administrar la
     cuenta desde la organización. En este laboratorio utilizo el rol por
     defecto `OrganizationAccountAccessRole`.

![Creación de la cuenta de Desarrollo](./screenshots/03-dev-account.png)

Cuando el asistente termina, AWS aprovisiona la nueva cuenta y la incorpora a la
organización. Tras unos instantes, la cuenta de Desarrollo aparece junto con la
Management Account y la cuenta de Producción:

![Tres cuentas en la organización: Management, Producción y Desarrollo](./screenshots/03-three-accounts.png)

> 💡 A diferencia del caso de una cuenta invitada, cuando la cuenta se crea
> directamente desde la Organization, el rol `OrganizationAccountAccessRole` se
> genera automáticamente.

### 4.2 Acceso a la cuenta de Desarrollo desde la Management Account

Igual que con Producción, la Management Account puede asumir el rol
`OrganizationAccountAccessRole` en la cuenta de Desarrollo para administrarla.

1. Desde la consola de la Management Account, uso la opción **Cambiar rol**.
2. Selecciono la cuenta de Desarrollo en la lista de cuentas disponibles y
   elijo el rol `OrganizationAccountAccessRole`.

![Selección de cuenta y rol para Desarrollo](./screenshots/03-switch-dev-role.png)

3. Una vez asumido el rol, la consola muestra que estoy operando dentro de la
   cuenta de Desarrollo bajo ese rol compartido:

![Rol asumido correctamente en la cuenta de Desarrollo](./screenshots/03-dev-role-switched.png)

4. Verifico que la cuenta de Desarrollo tiene el rol `OrganizationAccountAccessRole`
   creado automáticamente por AWS:

![Rol `OrganizationAccountAccessRole` presente en la cuenta de Desarrollo](./screenshots/03-dev-role-assigned.png)

---

## 5. Unidades Organizativas (OU)

Cuando ya tengo las cuentas creadas, el siguiente paso es **agruparlas en
Unidades Organizativas (Organizational Units, OU)**.

Las OUs permiten aplicar políticas y gobernanza de forma centralizada sobre
grupos de cuentas que comparten un propósito (Producción, Desarrollo, Sandbox,
etc.).

En este laboratorio creo dos OUs principales bajo la raíz (`Root`):

- `Producción`
- `Desarrollo`

> En la arquitectura final se añaden más OUs (por ejemplo, Seguridad o Sandbox).
> Aquí solo muestro el proceso con un ejemplo sencillo.

### 5.1 Creación de la OU de Producción

1. Desde la **Management Account**, accedo a **AWS Organizations** y abro la
   vista de **Organización**, donde se ve la estructura actual de cuentas bajo
   `Root`.
2. En el menú **Acciones**, selecciono **Unidad organizativa → Crear nueva**.

![Creación de una nueva OU bajo Root](./screenshots/03-pro-create-ou.png)

3. En el formulario de creación, indico el nombre de la OU. En este caso,
   `Producción`. Opcionalmente podría añadir etiquetas para mejorar la
   categorización y los reportes.

![Formulario de creación de la OU Producción](./screenshots/03-pro-create-ou-2.png)

4. Tras confirmar, la OU `Producción` aparece bajo `Root` en la estructura:

![OU Producción creada bajo Root](./screenshots/03-pro-ou-created.png)

### 5.2 Creación de la OU de Desarrollo

Del mismo modo creo la OU para **Desarrollo**:

1. Desde la misma vista de **Organización**, repito  
   **Unidad organizativa → Crear nueva**.
2. Asigno el nombre `Desarrollo` a la nueva OU.
3. La estructura organizativa pasa a mostrar ambas OUs (`Desarrollo` y
   `Producción`) bajo `Root`.

### 5.3 Situación inicial de las cuentas y objetivo de la reorganización

En este punto tengo tres cuentas principales:

- `AWS-GENERAL-JVELAZQUEZ` → Management Account (cuenta de administración).
- `AWS-PROD-JVELAZQUEZ` → cuenta de Producción.
- `AWS-DEV-JVELAZQUEZ` → cuenta de Desarrollo.

Inicialmente, todas están colgadas directamente de `Root`.  
El objetivo es **mover cada cuenta a su OU correspondiente** para reflejar mejor
su propósito y poder aplicar políticas diferentes en el futuro.

![Estructura inicial con las tres cuentas bajo Root y las dos OUs recién creadas](./screenshots/03-ou's-created.png)

### 5.4 Trasladar las cuentas a su OU correspondiente

1. Selecciono la cuenta que quiero mover en la vista de **Organización**.
2. En el menú **Acciones**, elijo **Cuenta de AWS → Trasladar**.
3. En la pantalla de traslado, se muestra la cuenta y la estructura de destino.
   Selecciono la OU adecuada y confirmo con  
   **Trasladar una cuenta de AWS**.
4. Repito el proceso para las cuentas de Desarrollo y Producción, moviéndolas a
   las OUs `Desarrollo` y `Producción`, respectivamente.

Al finalizar, la estructura queda organizada de esta forma:

![Estructura final con las cuentas bajo sus OUs correspondientes](./screenshots/03-final-ou.png)

---

## 6. Service Control Policies (SCP) – Ejemplo de denegación de IAM

Las **Service Control Policies (SCP)** definen los permisos máximos que pueden
llegar a tener los usuarios y roles de IAM dentro de las cuentas de la
organización.

En este apartado creo una SCP de ejemplo llamada `IAMDeny`, cuyo objetivo es
**denegar el uso del servicio IAM** en la cuenta de Producción.

> Importante: una SCP nunca concede permisos por sí misma; solo limita qué
> acciones podrían llegar a estar permitidas, incluso aunque una política de IAM
> conceda más permisos.

### 6.1 Habilitar las políticas de control de servicios

En una organización nueva, las SCP vienen deshabilitadas.  
Para poder usarlas:

1. Desde la **Management Account**, voy a  
   **AWS Organizations → Políticas → Políticas de control de servicios**.
2. Pulso en **Habilitar políticas de control de servicios**.

![Habilitar políticas de control de servicios](./screenshots/03-scp.png)

Tras habilitarlas, aparece la vista de **Políticas de control de servicios** con
las políticas disponibles:

![SCP habilitadas en la organización](./screenshots/03-scp-2.png)

### 6.2 Creación de la política `IAMDeny`

Ahora creo una SCP personalizada para denegar el uso de IAM:

1. En **Políticas de control de servicios**, selecciono **Crear política**.
2. Indico:
   - **Nombre de la política**: `IAMDeny`
   - **Descripción**: por ejemplo, `Denegación del uso de IAM en la cuenta de producción`.

3. En el editor de la política defino un único *statement*:

   - `Effect: Deny`
   - `Action: iam:*`
   - `Resource: *`

Es decir, la política niega **todas las acciones** del servicio IAM sobre
**cualquier recurso** dentro de la cuenta donde se aplique.

![Creación de la SCP IAMDeny](./screenshots/03-scp-3.png)

4. Guardo la política. `IAMDeny` aparece en la lista de políticas como
   “Política administrada por el cliente”:

![Política IAMDeny creada correctamente](./screenshots/03-scp-4.png)

### 6.3 Asociación de la SCP a la cuenta de Producción

El siguiente paso es **adjuntar** la política `IAMDeny` a la cuenta de Producción
(`AWS-PROD-JVELAZQUEZ`):

1. Selecciono la política `IAMDeny` en la lista.
2. En el menú **Acciones**, elijo **Asociar política**.
3. En la pantalla de selección de destinos, navego por la estructura de la
   organización y marco la cuenta de Producción dentro de la OU `Producción`.
4. Confirmo con **Asociar política**. Desde ese momento, la cuenta de Producción
   queda sujeta a la SCP `IAMDeny`.

![Asociar la política IAMDeny](./screenshots/03-scp-5.png)

### 6.4 Verificación del efecto de la SCP

Para comprobar el efecto real de la SCP:

1. Accedo a la **cuenta de Producción** asumiendo el rol
   `OrganizationAccountAccessRole` desde la Management Account (como en los
   apartados anteriores).
2. Dentro del servicio **IAM** en la cuenta de Producción, intento realizar
   operaciones básicas como `iam:GetAccountSummary` o `iam:ListAccountAliases`.

![Seleccionar la cuenta de Producción como destino](./screenshots/03-scp-6.png)

Debido a la SCP `IAMDeny`, estas acciones son **denegadas**, incluso aunque el
rol tenga permisos administrativos.  
La consola indica que *“a service control policy explicitly denies the action”*.

> Nota: este capítulo muestra un **ejemplo práctico** de uso de una SCP sobre una
> cuenta concreta. En el capítulo de arquitectura se documentan todas las SCP
> finales y su aplicación sobre OUs y cuentas.

---

## 7. IAM Identity Center – Configuración inicial

En esta sección habilito **IAM Identity Center** para la organización y configuro
un primer conjunto de permisos de tipo administrador, junto con un usuario de
prueba.

El objetivo es ver el flujo completo:

1. Habilitar la instancia.
2. Crear el conjunto de permisos.
3. Crear un grupo y un usuario.
4. Asignar el grupo a las cuentas de la organización.
5. Validar el acceso desde el portal.

---

### 7.1 Habilitar la instancia de IAM Identity Center

1. Desde la **Management Account**, accedo a **IAM Identity Center**.
2. En la pantalla de bienvenida, AWS explica el servicio y ofrece habilitar una
   instancia para la organización.

![Pantalla para habilitar IAM Identity Center](./screenshots/03-identity-center.png)

3. Confirmo la **región** donde quiero crear la instancia (en el laboratorio,
   `us-east-1`) y dejo la configuración avanzada por defecto.
4. Pulso **Activar** para crear la instancia asociada a mi AWS Organization.

Tras unos segundos, aparece la página de configuración de la instancia:

![Instancia de IAM Identity Center creada para la organización](./screenshots/03-identity-center-3.png)

Aquí puedo ver:

- El **ID de la organización**.
- El **ARN de la instancia** de Identity Center.
- La región usada.
- La **URL del portal de acceso** por defecto.

---

### 7.2 Configuración de la fuente de identidad y del portal de acceso

Para este laboratorio mantengo la configuración por defecto:

- **Fuente de identidad**: *Directorio de Identity Center*.  
  Gestiono usuarios y grupos directamente desde IAM Identity Center.
- **Método de autenticación**: contraseña.

En un entorno corporativo podría integrar un IdP externo (Azure AD, Okta, etc.),
pero para el laboratorio no es necesario.

A continuación personalizo la URL del portal de acceso:

1. Desde el panel de IAM Identity Center, en **Configuración de IAM Identity Center**,  
   edito la **URL de AWS access portal**.
2. Indico el subdominio `josevelazquez` y lo confirmo.

![Personalización de la URL del AWS access portal](./screenshots/03-identity-center-4.png)

A partir de aquí, los usuarios accederán al portal mediante:

`https://josevelazquez.awsapps.com/start`

---

### 7.3 Creación de un conjunto de permisos predefinido `AdministratorAccess`

Ahora creo el **conjunto de permisos** que usarán los administradores.

1. En IAM Identity Center, voy a  
   **Permisos para varias cuentas → Conjuntos de permisos** y hago clic en
   **Crear conjunto de permisos**.
2. En el **Paso 1 – Seleccionar el tipo de conjunto de permisos**:
   - Elijo **Conjunto de permisos predefinido**.
   - Selecciono la política administrada por AWS **`AdministratorAccess`**.

![Selección de tipo de conjunto de permisos y política AdministratorAccess](./screenshots/03-identity-center-5.png)

3. En el **Paso 2 – Especificar los detalles del conjunto de permisos**, dejo:

   - **Nombre**: `AdministratorAccess` (por defecto).
   - **Descripción** (opcional): `Conjunto de permisos administrativo para el laboratorio`.
   - **Duración de la sesión**: 4 horas.
   - El resto de opciones las dejo sin configurar.

![Detalles del conjunto de permisos AdministratorAccess](./screenshots/03-identity-center-6.png)

4. En el **Paso 3 – Revisar y crear**, reviso el resumen y pulso **Crear**.

![Revisión final antes de crear el conjunto de permisos](./screenshots/03-identity-center-7.png)

Con esto queda creado el conjunto de permisos `AdministratorAccess` en Identity
Center. Más adelante lo asigno a las cuentas de Desarrollo y Producción.

---

### 7.4 Creación del grupo de administradores

Para centralizar los permisos creo un grupo que representará al equipo de
administración.

1. En **IAM Identity Center → Grupos**, selecciono **Crear grupo**.
2. Relleno:

   - **Nombre del grupo**: `Administradores`
   - **Descripción** (opcional): `Grupo de administradores con acceso a las cuentas del laboratorio`.

3. De momento no añado usuarios (el grupo puede crearse vacío).  
   Pulso **Crear grupo**.

![Creación del grupo Administradores](./screenshots/03-identity-center-10.png)

---

### 7.5 Creación del usuario "Alicia" y asignación al grupo

Ahora creo un usuario de ejemplo y lo añado al grupo `Administradores`.

1. En **IAM Identity Center → Usuarios**, selecciono **Agregar usuario**.
2. En el **Paso 1 – Especificar los detalles del usuario**, indico:

   - **Nombre de usuario**: `Alicia`
   - **Dirección de correo electrónico**:  
     `jvelazquez.aws.labs+alicia@gmail.com`
   - **Nombre**: `Alicia`
   - **Apellidos**: `Gimenez`
   - Opción **Genere una contraseña de un solo uso** para que el sistema genere
     una contraseña temporal.

![Creación del usuario Alicia](./screenshots/03-identity-center-8.png)

3. En el **Paso 2 – Agregar usuario a grupos (opcional)**, selecciono el grupo
   `Administradores`.

![Asignar el usuario Alicia al grupo Administradores](./screenshots/03-identity-center-12.png)

4. En el **Paso 3 – Revisar y agregar usuario**, confirmo y creo el usuario.

Al finalizar, Identity Center muestra una ventana con la **contraseña de uso
único** y la URL del portal. Esta información es la que se entrega al usuario:

![Contraseña de un solo uso generada para Alicia](./screenshots/03-identity-center-13.png)

- **URL del portal**: `https://josevelazquez.awsapps.com/start`
- **Usuario**: `Alicia`
- **Contraseña de uso único**: generada automáticamente.

---

### 7.6 Asignar el grupo Administradores a las cuentas de AWS

Ya tengo:

- El conjunto de permisos `AdministratorAccess`.
- El grupo `Administradores`.
- El usuario `Alicia` dentro de ese grupo.

Ahora asigno el grupo a las cuentas de AWS que forman parte del laboratorio.

1. En IAM Identity Center, voy a  
   **Permisos para varias cuentas → Cuentas de AWS**.
2. Se muestra el árbol de la organización con las OUs y las cuentas. Selecciono:

   - `AWS-DEV-JVELAZQUEZ` (Desarrollo).
   - `AWS-PROD-JVELAZQUEZ` (Producción).

![Selección de cuentas de AWS para la asignación](./screenshots/03-identity-center-14.png)

3. Pulso **Asignar personas o grupos**.  
   En el **Paso 1 – Seleccionar personas y grupos**, voy a la pestaña **Grupos**
   y marco `Administradores`.

![Seleccionar el grupo Administradores para la asignación](./screenshots/03-identity-center-15.png)

4. En el **Paso 2 – Seleccionar conjuntos de permisos**, selecciono el conjunto
   `AdministratorAccess`.

![Seleccionar el conjunto de permisos AdministratorAccess](./screenshots/03-identity-center-16.png)

5. En el **Paso 3 – Revisar y enviar**, reviso el resumen:

   - Grupo: `Administradores`.
   - Cuentas de AWS: `AWS-DEV-JVELAZQUEZ` y `AWS-PROD-JVELAZQUEZ`.
   - Conjunto de permisos: `AdministratorAccess`.

   Finalmente pulso **Enviar**.

![Revisión final de la asignación del grupo a las cuentas](./screenshots/03-identity-center-17.png)

A partir de este momento, cualquier usuario que añada al grupo `Administradores`
(como `Alicia`) podrá:

- Entrar en el portal de acceso:  
  `https://josevelazquez.awsapps.com/start`.
- Ver las cuentas de Desarrollo y Producción.
- Acceder a ellas con permisos de `AdministratorAccess`.

---

### 7.7 Verificación de acceso desde el portal como usuaria "Alicia"

Para confirmar que todo funciona correctamente, realizo una prueba de acceso
real con el usuario `Alicia`.

1. Desde un navegador, abro la URL del portal:

   `https://josevelazquez.awsapps.com/start`

   En la pantalla de inicio de sesión indico el **nombre de usuario** `Alicia` y
   pulso **Siguiente**.

![Inicio de sesión en el portal con el usuario Alicia](./screenshots/03-identity-center-18.png)

2. Introduzco la **contraseña de un solo uso** generada anteriormente y pulso
   **Iniciar sesión**.

![Introducción de la contraseña del usuario Alicia](./screenshots/03-identity-center-19.png)

3. Si todo es correcto, el portal muestra las **cuentas a las que Alicia tiene
   acceso**. En este laboratorio aparecen:

   - `AWS-DEV-JVELAZQUEZ` (Desarrollo)
   - `AWS-PROD-JVELAZQUEZ` (Producción)

   En ambas se ofrece el conjunto de permisos `AdministratorAccess`, que es el
   que asocié al grupo `Administradores`.

![Portal de acceso mostrando las cuentas DEV y PROD con AdministratorAccess](./screenshots/03-identity-center-20.png)

Con esta prueba verifico que:

- El usuario `Alicia` se autentica correctamente en el portal de Identity Center.
- El grupo `Administradores` está asociado al conjunto de permisos
  `AdministratorAccess`.
- Las asignaciones del grupo a las cuentas de Desarrollo y Producción funcionan
  como se esperaba, ya que Alicia puede acceder a ambas con permisos de
  administrador.

---




