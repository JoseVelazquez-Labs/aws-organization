# 03 - Implementation

## 1. Prerrequisitos

- Cuenta de AWS con permisos de `OrganizationAccountAccessRole` o equivalente.
- Billing correctamente configurado.
- (Opcional) Terraform instalado (`>= 1.6`) si se utiliza IaC.

## 2. Creación de la AWS Organization

1. Desde un usuario IAM con los permisos necesarios, evitando así el uso del usuario root cuando no es imprescindible, nos dirigimos a **AWS Organizations**.
   
2. Seleccionamos **Crear una organización**.

![Pantalla de creación de la Organization](./screenshots/03-create-organization.png)

3. Acto seguido, se habrá creado la organización y observaremos su estructura, que irá creciendo en función del número de cuentas a gestionar.

![Pantalla de creación de la Organization](./screenshots/03-organization-created.png)

## 3. Gestión de cuentas en la AWS Organization

1. Seleccionamos **Agregar una cuenta de AWS**. Indicamos el ID de la cuenta a invitar, o bien, el correo electrónico.
En mi caso, he creado una cuenta root de producción para añadirla a esta organización. Clicamos en **Enviar petición**

![Pantalla de creación de la Organization](./screenshots/03-invite-account.png)

2. Ahora nos dirigimos a la consola de administración de AWS con la cuenta que previamente hemos invitado. Una vez hayamos entrado en AWS Organizations, verificaremos que hemos recibido la invitación:

![Pantalla de creación de la Organization](./screenshots/03-account-invited.png)

3.  Una vez hemos aceptado la invitación, volvemos a la cuenta de administración y comprobamos como se ha añadido la cuenta de producción en la organización:

![Pantalla de creación de la Organization](./screenshots/03-two-accounts.png)

4. A continuación, procedo a crear un rol en el servicio IAM para permitir que mi cuenta general disponga de permisos de administrador sobre la nueva cuenta de producción que hemos añadido a la organización. Este paso se realiza desde la cuenta de producción. Para ello, seleccionaremos **Cuenta de AWS** como tipo de entidad e indicaremos el ID de la misma.

> Apunte: Cuando creamos la cuenta directamente desde la organización, en lugar de invitar a una cuenta existente como este caso, el rol **OrganizationAccountAccessRole** se crea de manera automática.

![Pantalla de creación de la Organization](./screenshots/03-iam-rol.png)

Agregaremos la política de permisos **AdministratorAccess**, y por último, creamos el rol **OrganizationAccountAccessRole**.

![Pantalla de creación de la Organization](./screenshots/03-iam-rol-2.png)

5. Una vez se ha creado el rol, probaremos desde nuestra cuenta IAM, perteneciente a la cuenta de administración, a acceder a la cuenta de producción.

![Pantalla de creación de la Organization](./screenshots/03-change-rol.png)

![Pantalla de creación de la Organization](./screenshots/03-change-rol-2.png)

![Pantalla de creación de la Organization](./screenshots/03-rol-changed.png)

> A partir de este momento, la cuenta actual actúa como **Management Account**.

