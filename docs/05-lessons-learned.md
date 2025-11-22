# 05 - Lessons learned

En este capítulo, redacto un pequeño resumen de los aprendizajes y posibles mejoras que considero más destacables.

---

## 1. Gobernanza

- **Multi-cuenta como norma, no como excepción**: separar Prod, Dev y Sandbox en cuentas distintas simplifica permisos, auditoría y costes.
- **Las OUs son el esqueleto**: Security, Infrastructure, Workloads y Sandbox me obligan a pensar en “tipos de cuentas” y no en cuentas sueltas.
- **SCP como “marco de juego”**:
  - Restricción de regiones.
  - Limitación del root.
  - Controles de coste en Sandbox.  
  IAM da permisos; las SCP marcan hasta dónde pueden llegar.

---

## 2. Servicios utilizados

### 2.1 AWS Organizations
- Punto central para crear cuentas, agruparlas en OUs y aplicar SCP.
- Diferencia práctica entre **invitar** cuentas existentes y **crearlas** desde la organización con `OrganizationAccountAccessRole` automático.

### 2.2 IAM / IAM Identity Center
- Paso de usuarios IAM sueltos a un modelo con:
  - Usuarios y grupos en Identity Center.
  - Permission sets asignados a cuentas.
- Más cercano al SSO corporativo y al control por rol de trabajo (RBAC).

### 2.3 Service Control Policies
- Útiles para controles globales (regiones, root, sandbox).
- Importante probar primero en Sandbox: una SCP agresiva puede bloquear una cuenta entera.

---

## 3. Pensando en una empresa real

Si este diseño creciera en producción, tendría sentido:

- **Refinar roles de negocio**: no solo “Administradores”, sino grupos para Dev, Ops, Seguridad, Finanzas, etc.
- **Procesos de alta/baja** integrados con un directorio corporativo y revisiones periódicas de acceso.
- **Estandarizar naming y tagging** para costes, reporting y automatización.

---

## 4. Posibles mejoras

### 4.1 AWS Control Tower
- Probar Control Tower como landing zone gestionada:
  - Cuentas de seguridad predefinidas.
  - Guardrails listos.
  - Integración nativa con Organizations e Identity Center.
- Comparar su enfoque con la configuración manual del laboratorio.

### 4.2 Monitorización y seguridad

- Centralizar logs con CloudTrail organizacional y una cuenta `log-archive`.
- Añadir servicios como:
  - AWS Config, GuardDuty, Security Hub.
  - Herramientas de terceros para métricas y trazas (Datadog, etc.).

### 4.3 Automatización e IaC

- Describir OUs, cuentas y SCP con Terraform / CloudFormation.
- Gestionar cambios vía GitHub (pull requests + pipelines), como haría un equipo de plataforma.

---

## 5. Conclusión

Este laboratorio me ha ayudado a:

- Entender de verdad la **gobernanza multi-cuenta en AWS**.
- Ganar soltura con **Organizations, SCP e IAM Identity Center**.
- Pensar como alguien que diseña guardrails y estructuras de acceso, no solo como alguien que despliega recursos.

Quedan muchas mejoras (Control Tower, observabilidad avanzada, IaC), pero la base es sólida para mi portfolio y para dar el salto a proyectos cloud más serios.
