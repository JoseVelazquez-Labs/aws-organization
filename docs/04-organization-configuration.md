# 04 - Configuración actual de la organización

En este punto describo el **estado real** de la organización que he montado en AWS:

- Qué cuentas existen ahora mismo.
- Cómo están distribuidas en las OUs.
- Qué Service Control Policies (SCP) estoy usando.

En `02-architecture.md` explico el diseño objetivo y en `03-implementation.md`
muestro el “how-to” para crear recursos concretos.  
Aquí enseño el **resultado actual del laboratorio**.

---

## 4.1 Estructura de cuentas y OUs

La organización está estructurada de la siguiente forma:

- **Management Account**
  - `AWS-GENERAL-JVELAZQUEZ`  
    - Cuenta de administración: billing, configuración global y capa de gobernanza.

- **OU Seguridad**
  - Por ahora sin cuentas.  
    - Pensada para futuras cuentas de `log-archive` y `audit`, donde centralizar los logs y las tareas de seguridad.

- **OU Infraestructura**
  - Por ahora sin cuentas.  
    - Pensada para servicios compartidos (bastion, ECR común, herramientas internas, etc.).

- **OU Workloads**
  - **Sub-OU Producción**
    - `AWS-PROD-JVELAZQUEZ` → entorno de **producción**.
  - **Sub-OU Desarrollo**
    - `AWS-DEV-JVELAZQUEZ` → entorno de **desarrollo / no producción**.

- **OU Sandbox**
  - `PERSONAL-SANDBOX-JVELAZQUEZ` → entorno de **pruebas personales**.

![Organización creada](./screenshots/03-organization.png)

Resumen en tabla:

| Nivel / OU             | Cuenta                         | Estado actual | Uso principal                               |
|------------------------|--------------------------------|--------------|--------------------------------------------|
| Root                   | `AWS-GENERAL-JVELAZQUEZ`       | Creada       | Management, billing, gobernanza.           |
| Seguridad              | *(sin cuentas aún)*            | Creada       | Futuras cuentas de logs / auditoría.       |
| Infraestructura        | *(sin cuentas aún)*            | Creada       | Futuros servicios compartidos.             |
| Workloads / Producción | `AWS-PROD-JVELAZQUEZ`          | Creada       | Entorno de producción.                     |
| Workloads / Desarrollo | `AWS-DEV-JVELAZQUEZ`           | Creada       | Entorno de desarrollo / pruebas.           |
| Sandbox                | `PERSONAL-SANDBOX-JVELAZQUEZ`  | Creada       | Entorno de sandbox personal.               |

Con esta estructura ya tengo separados Prod, Dev y Sandbox, y dejo preparadas las OUs
de Seguridad e Infraestructura para cuando quiera crecer.

---

## 4.2 SCP implementadas

Sobre esta estructura he creado varias **Service Control Policies (SCP)** para empezar
a aplicar gobernanza:

![SCP implementadas](./screenshots/03-all-scp.png)

1. **`SCP-Restrict-Regions`**  
   - Objetivo: permitir solo un conjunto de regiones aprobadas (por ejemplo, `eu-west-1`).  
   - Idea: evitar despliegues accidentales en regiones que no quiero usar.

2. **`SCP-Restrict-Root`**  
   - Objetivo: limitar al máximo el uso del usuario **root**.  
   - Idea: el root no se utiliza para el trabajo diario, solo para tareas puntuales.

3. **`SCP-Protect-CloudTrail`**  
   - Objetivo: proteger CloudTrail (no poder pararlo, borrarlo o modificarlo) en las cuentas de seguridad.  
   - De momento está pensada para cuando cree `log-archive` y `audit`.

4. **`SCP-Limit-Sandbox-Costs`**  
   - Objetivo: reducir el riesgo de costes altos en la OU `Sandbox`.  
   - Idea: bloquear ciertos recursos caros (por ejemplo, algunos tipos de instancias EC2 grandes o RDS) en la cuenta de sandbox.

> La SCP `IAMDeny` que aparece en `03-implementation.md` la utilizo solo como **ejemplo de laboratorio**
> para aprender cómo funcionan las SCPs. No forma parte del diseño final, porque bloquea todo IAM
> y es demasiado agresiva para un entorno real.

---

## 4.3 Mapa de SCP por OU y cuenta

En la tabla siguiente resumo qué SCP se aplican en cada parte de la organización.

- ✅ → la SCP se aplica (o está planificada para aplicarse) a esa OU / cuenta.  
- ❌ → la SCP no se aplica ahí.

| OU / Cuenta                 | SCP-Restrict-Regions | SCP-Restrict-Root | SCP-Protect-CloudTrail                        | SCP-Limit-Sandbox-Costs |
|----------------------------|----------------------|-------------------|-----------------------------------------------|--------------------------|
| Root                       | ✅ (modo global)     | ✅ (modo global)  | ❌                                            | ❌                       |
| Seguridad (OU)             | ✅                   | ✅                | ✅                                            | ❌                       |
| Infraestructura (OU)       | ✅                   | ✅                | ❌                                            | ❌                       |
| Workloads / Producción (OU)| ✅                   | ✅                | ❌ (los logs se centralizarán en Seguridad)   | ❌                       |
| Workloads / Desarrollo (OU)| ✅                   | ✅                | ❌                                            | ❌                       |
| Sandbox / PERSONAL-SANDBOX | ✅                   | ✅                | ❌                                            | ✅                       |

- **Restrict-Regions** y **Restrict-Root** se aplican de forma amplia (Root y OUs principales),
  porque son reglas base que quiero prácticamente en toda la organización.
- **Protect-CloudTrail** está pensada para las futuras cuentas de Seguridad, donde tendrá sentido
  proteger los trails que centralicen los logs.
- **Limit-Sandbox-Costs** solo se aplica en la OU `Sandbox`, que es donde hago pruebas y quiero
  limitar ciertos recursos caros.

---

## 4.4 Qué aporta esta configuración

Con esta combinación de cuentas, OUs y SCP consigo:

- **Separación de entornos**  
  Producción, desarrollo y sandbox viven en cuentas distintas y en OUs diferentes.  
  Si hay un problema en Desarrollo o Sandbox, no debería afectar a Producción.

- **Reglas base comunes**  
  - Las cuentas están limitadas a las regiones que he definido (`SCP-Restrict-Regions`).  
  - El uso del usuario root queda bastante acotado (`SCP-Restrict-Root`).

- **Primeros pasos de seguridad y control de costes**  
  - Diseño preparado para proteger CloudTrail en las cuentas de Seguridad.  
  - Sandbox con limitaciones de recursos caros para no llevarme sorpresas en la factura.

- **Arquitectura preparada para crecer**  
  Cuando cree `log-archive`
