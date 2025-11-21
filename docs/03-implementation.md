# 03 - Implementation (How-to)

En este apartado no se describe la arquitectura final de la organización, sino un **how-to** práctico sobre cómo crear y configurar, paso a paso, los recursos básicos que se usarán más adelante:

- La AWS Organization.
- Cuentas miembro (producción y desarrollo).
- Unidades Organizativas (OU).
- Una Service Control Policy (SCP) de ejemplo.

El diseño completo de la organización (todas las cuentas, OUs y políticas) se detalla en el capítulo siguiente.

---

## 1. Prerrequisitos

Antes de empezar, es necesario contar con:

- Una cuenta de AWS que actuará como **Management Account**, con permisos administrativos sobre **AWS Organizations**.
- La facturación (Billing) correctamente configurada en la Management Account.
- Acceso a la cuenta que se va a invitar como **Producción**, evitando el uso del usuario root salvo para tareas imprescindibles.

---

## 2. Creación de la AWS Organization

El primer paso es crear una **AWS Organization** a partir de la cuenta que actuará como **Management Account**. Esta cuenta será la responsable de la gobernanza global, la facturación consolidada y la gestión de las cuentas miembro.

1. Desde un usuario IAM con los permisos necesarios en la Management Account (evitando así el uso del usuario root cuando no es imprescindible), accedemos al servicio **AWS Organizations**.
2. Seleccionamos **Crear una organización**.

![Pantalla de creación de la Organization](./screenshots/03-create-organization.png)

3. Una vez creada la organización, podremos ver la estructura inicial, que irá creciendo en función del número de cuentas que gestionemos desde aquí.

![Organización creada con la Management Account](./screenshots/03-organization-created.png)

---

## 3. Gestión de cuentas en la AWS Organization

En este laboratorio, además de la Management Account, se añade una cuenta adicional que simula el entorno de **Producción**. Para ello, se invita una cuenta existente a la organización y se configura un rol que permite a la Management Account administrarla.

> Más adelante, en el diseño final, se añadirán más cuentas y se detallará cómo quedan
> organizadas. Aquí nos centramos en **un ejemplo representativo** del flujo.

### 3.1 Invitación de la cuenta de Producción

1. Desde la Management Account, en el panel de **AWS Organizations**, seleccionamos **Agregar una cuenta de AWS**.
2. Indicamos el **ID de la cuenta** a invitar o el **correo electrónico** asociado.  
   En este caso, se ha creado previamente una cuenta de producción para añadirla a la organización. A continuación, pulsamos en **Enviar petición**.

![Invitar una cuenta a la organización](./screenshots/03-invite-account.png)

3. Ahora accedemos a la consola de AWS con la cuenta invitada. Una vez dentro de **AWS Organizations**, comprobamos que aparece la invitación pendiente:

![Invitación recibida en la cuenta invitada](./screenshots/03-account-invited.png)

4. Tras aceptar la invitación desde la cuenta de producción, volvemos a la Management Account y verificamos que la cuenta de producción se ha añadido correctamente a la organización:

![Dos cuentas en la organización: Management y Producción](./screenshots/03-two-accounts.png)

### 3.2 Creación del rol de acceso entre cuentas

Para que la Management Account pueda administrar la cuenta de producción sin utilizar credenciales propias de esa cuenta, se crea un **rol de IAM** en la cuenta de producción que podrá ser asumido desde la Management Account.

Este paso se realiza **desde la cuenta de Producción**:

1. En el servicio **IAM**, accedemos a **Roles** → **Create role**.
2. Elegimos **Cuenta de AWS** como tipo de entidad confiable e indicamos el ID de la  
   Management Account. De esta forma, solo esa cuenta podrá asumir el rol.

> Apunte: cuando creamos la cuenta directamente desde la organización, en lugar de invitar
> a una cuenta existente como en este caso, el rol `OrganizationAccountAccessRole`
> se crea de manera automática.

![Creación del rol confiando en la Management Account](./screenshots/03-iam-role.png)

3. Asignamos la política de permisos **AdministratorAccess**, ya que en este laboratorio queremos que la Management Account tenga control administrativo completo sobre la cuenta de producción. En un entorno real podría restringirse a un subconjunto más específico de permisos.
4. Por último, creamos el rol con el nombre `OrganizationAccountAccessRole`.

![Rol `OrganizationAccountAccessRole` con AdministratorAccess](./screenshots/03-iam-role-2.png)

### 3.3 Prueba de acceso desde la Management Account

Para validar que la configuración es correcta, probamos a asumir el rol recién creado desde un usuario IAM de la Management Account:

1. Desde la consola de la Management Account, abrimos el menú de **Cambiar rol**.

![Selección de cuenta y rol](./screenshots/03-switch-pro-role.png)

2. Indicamos la cuenta de producción y el rol `OrganizationAccountAccessRole`, y pulsamos en **Cambiar función**.

![Confirmación de cambio de rol](./screenshots/03-switch-pro-role-2.png)

3. Tras asumir el rol, la consola mostrará claramente que estamos operando dentro de la cuenta de producción utilizando el rol compartido, sin necesidad de utilizar credenciales directas de esa cuenta.

![Rol asumido correctamente en la cuenta de Producción](./screenshots/03-pro-role-switched.png)

---

## 4. Aprovisionamiento de nuevas cuentas desde AWS Organizations

En el apartado anterior se ha visto cómo **invitar una cuenta existente** a la organización y crear manualmente el rol `OrganizationAccountAccessRole`.  

En este caso, se opta por un enfoque distinto: **crear una nueva cuenta directamente desde AWS Organizations**, lo que simplifica parte del proceso y permite estandarizar la forma en la que se añaden cuentas de forma más automatizable.

En este ejemplo, la nueva cuenta representará un entorno de **Desarrollo**.

### 4.1 Creación de la cuenta de Desarrollo desde la Organization

1. Desde la **Management Account**, accedemos al servicio **AWS Organizations**.
2. En la sección de cuentas, seleccionamos la opción **Crear una cuenta de AWS**.
3. Indicamos:
   - Nombre de la cuenta (por ejemplo, `Development`).
   - Correo electrónico asociado a la nueva cuenta.
   - (Opcional) Un rol de IAM que se creará automáticamente para administrar la cuenta
     desde la organización. En este laboratorio se utiliza el rol por defecto  
     `OrganizationAccountAccessRole`.

![Creación de la cuenta de Desarrollo](./screenshots/03-dev-account.png)

Una vez completado el asistente, AWS aprovisiona la nueva cuenta y la incorpora a la organización. Tras unos instantes, podemos comprobar que la cuenta de desarrollo ya aparece junto con la Management Account y la cuenta de producción:

![Tres cuentas en la organización: Management, Producción y Desarrollo](./screenshots/03-three-accounts.png)

> 💡 A diferencia del caso anterior (invitando una cuenta existente), cuando la cuenta se
> crea directamente desde la Organization, el rol `OrganizationAccountAccessRole` se
> genera automáticamente en la nueva cuenta.

### 4.2 Acceso a la cuenta de Desarrollo desde la Management Account

Al igual que con la cuenta de producción, la Management Account puede asumir el rol `OrganizationAccountAccessRole` en la cuenta de desarrollo para administrarla sin utilizar credenciales propias de esa cuenta.

1. Desde la consola de la Management Account, utilizamos la opción de **Cambiar rol**.
2. Seleccionamos la cuenta de desarrollo en la lista de cuentas disponibles y escogemos el rol `OrganizationAccountAccessRole`.

![Selección de cuenta y rol para Desarrollo](./screenshots/03-switch-dev-role.png)

3. Una vez asumido el rol, la consola mostrará que estamos operando dentro de la cuenta de desarrollo bajo el rol compartido:

![Rol asumido correctamente en la cuenta de Desarrollo](./screenshots/03-dev-role-switched.png)

4. Por último, verificamos que, de manera automática, la cuenta de desarrollo dispone del rol `OrganizationAccountAccessRole` creado por AWS al aprovisionar la cuenta:

![Rol `OrganizationAccountAccessRole` presente en la cuenta de Desarrollo](./screenshots/03-dev-role-assigned.png)

---

## 5. Unidades Organizativas (OU)

Una vez creadas las cuentas que formarán parte de la organización, el siguiente paso consiste en **agruparlas en Unidades Organizativas (Organizational Units, OU)**.  

Las OUs permiten aplicar políticas y gobernanza de forma centralizada a conjuntos de cuentas que comparten un mismo propósito (por ejemplo, Producción, Desarrollo, Sandbox, etc.).

En este laboratorio se han creado dos OUs principales bajo la raíz (`Root`):

- `Producción`
- `Desarrollo`

> En el diseño final se podrán añadir más OUs (por ejemplo, Seguridad o Sandbox) según las
> necesidades de la organización. Aquí se muestra el proceso con un ejemplo sencillo.

### 5.1 Creación de la OU de Producción

1. Desde la **Management Account**, accedemos a **AWS Organizations** y a la vista de  
   **Organización**, donde se muestra la estructura actual de cuentas bajo `Root`.
2. En el menú **Acciones**, seleccionamos **Unidad organizativa → Crear nueva**.

![Creación de una nueva OU bajo Root](./screenshots/03-pro-create-ou.png)

3. En el formulario de creación, indicamos el nombre de la OU. En este caso, se crea  
   la OU llamada `Producción`. Opcionalmente, se podrían añadir etiquetas para una  
   mejor categorización y reporting.

![Formulario de creación de la OU Producción](./screenshots/03-pro-create-ou-2.png)

4. Tras confirmar la operación, la OU `Producción` aparece bajo `Root` en la estructura  
   organizativa:

![OU Producción creada bajo Root](./screenshots/03-pro-ou-created.png)

### 5.2 Creación de la OU de Desarrollo

Del mismo modo, se crea una segunda OU destinada a entornos de **Desarrollo**.

1. Desde la misma vista de **Organización**, repetimos el proceso  
   **Unidad organizativa → Crear nueva**.
2. Asignamos el nombre `Desarrollo` a esta nueva OU.
3. Una vez creada, la estructura organizativa muestra ambas OUs (`Desarrollo` y  
   `Producción`) bajo `Root`.

### 5.3 Situación inicial de las cuentas y objetivo de la reorganización

En este punto, la organización cuenta con tres cuentas principales:

- `AWS-GENERAL-JVELAZQUEZ` → Management Account (cuenta de administración).
- `AWS-PROD-JVELAZQUEZ` → cuenta destinada a producción.
- `AWS-DEV-JVELAZQUEZ` → cuenta destinada a desarrollo.

Inicialmente, todas las cuentas se encuentran directamente bajo `Root`.  
El objetivo es **mover las cuentas a la OU que les corresponde** para reflejar mejor su propósito y poder aplicar políticas diferenciadas en el futuro.

![Estructura inicial con las tres cuentas bajo Root y las dos OUs recién creadas](./screenshots/03-ou's-created.png)

### 5.4 Trasladar las cuentas a su OU correspondiente

1. Seleccionamos la cuenta que queremos mover en la vista de **Organización**.
2. En el menú **Acciones**, elegimos **Cuenta de AWS → Trasladar**.
3. En la pantalla de traslado, se muestra la cuenta que se va a mover y la estructura de destino disponible. Seleccionamos la OU correspondiente como destino y confirmamos con **Trasladar una cuenta de AWS**.
4. Repetimos el proceso para las cuentas de desarrollo y producción, moviéndolas a las OUs `Desarrollo` y `Producción`, respectivamente.

Tras completar el proceso, la estructura queda organizada de la siguiente manera:


![Estructura final con las cuentas bajo sus OUs correspondientes](./screenshots/03-final-ou.png)

---

## 6. Service Control Policies (SCP) – Ejemplo de denegación de IAM

Las **Service Control Policies (SCP)** permiten definir los permisos máximos que pueden tener los usuarios y roles de IAM dentro de las cuentas de una organización.  

En este apartado se crea una SCP de ejemplo llamada `IAMDeny`, cuyo objetivo es **denegar el uso del servicio IAM** en la cuenta de producción.

> Importante: una SCP no concede permisos por sí misma; únicamente limita qué acciones
> pueden llegar a estar permitidas, incluso si una política de IAM concede más permisos.

### 6.1 Habilitar las políticas de control de servicios

Por defecto, las SCP vienen deshabilitadas en una nueva organización. 
Para poder utilizarlas:

1. Desde la **Management Account**, accedemos a  
   **AWS Organizations → Políticas → Políticas de control de servicios**.
2. Pulsamos en **Habilitar políticas de control de servicios**.

![Habilitar políticas de control de servicios](./screenshots/03-scp.png)

Tras habilitarlas, aparece la vista de **Políticas de control de servicios** con las  
políticas disponibles:

![SCP habilitadas en la organización](./screenshots/03-scp-2.png)

### 6.2 Creación de la política `IAMDeny`

A continuación, se crea una SCP personalizada para denegar el uso de IAM:

1. En la sección de **Políticas de control de servicios**, seleccionamos **Crear política**.
2. Indicamos:
   - **Nombre de la política**: `IAMDeny`
   - **Descripción**: por ejemplo, `Denegación del uso de IAM en la cuenta de producción`.

3. En el editor de la política definimos un único *statement* con:
   - `Effect: Deny`
   - `Action: iam:*`
   - `Resource: *`

Es decir, la política niega **todas las acciones** del servicio IAM sobre **cualquier recurso** dentro de la cuenta a la que se aplique.

![Creación de la SCP IAMDeny](./screenshots/03-scp-3.png)

4. Guardamos la política. La nueva SCP `IAMDeny` aparece ahora en la lista de políticas
disponibles como “Política administrada por el cliente”:

![Política IAMDeny creada correctamente](./screenshots/03-scp-4.png)

### 6.3 Asociación de la SCP a la cuenta de Producción

El siguiente paso es **adjuntar** la política `IAMDeny` a la cuenta de producción  
(`AWS-PROD-JVELAZQUEZ`):

1. Seleccionamos la política `IAMDeny` en la lista.
2. En el menú **Acciones**, elegimos **Asociar política**.
3. En la pantalla de selección de destinos, navegamos por la estructura de la  
   organización y marcamos la cuenta de producción dentro de la OU `Producción`.
4. Confirmamos con **Asociar política**. A partir de este momento, la cuenta de producción queda sujeta a la SCP `IAMDeny`.
   ![Asociar la política IAMDeny](./screenshots/03-scp-5.png)

### 6.4 Verificación del efecto de la SCP

Para comprobar el efecto de la SCP:

1. Accedemos a la **cuenta de Producción** asumiendo el rol `OrganizationAccountAccessRole` desde la Management Account, tal y como se ha descrito en apartados anteriores.
2. Una vez dentro de la consola de IAM en la cuenta de producción, intentamos realizar una operación básica, como visualizar el resumen de la cuenta (`iam:GetAccountSummary`) o listar alias de cuenta (`iam:ListAccountAliases`).

![Seleccionar la cuenta de Producción como destino](./screenshots/03-scp-6.png)

Debido a la SCP `IAMDeny`, estas acciones son **denegadas**, incluso aunque el rol tenga permisos administrativos. La consola muestra un mensaje de error indicando que “a service control policy explicitly denies the action”.

---

> Nota: este capítulo muestra un **ejemplo práctico** de creación y uso de SCP sobre una
> cuenta concreta. En el capítulo de diseño se recogerán todas las SCP finales utilizadas
> y cómo se aplican sobre OUs y cuentas de la organización completa.
