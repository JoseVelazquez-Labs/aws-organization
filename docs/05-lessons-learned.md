# 05 - Lessons learned

En este capítulo resumo los principales aprendizajes del laboratorio y hacia dónde podría evolucionar.

---

## 1. Gobernanza

- **Multi-cuenta desde el principio**  
  Separar Prod, Dev y Sandbox en cuentas distintas simplifica permisos, trazabilidad y control de costes.

- **Las OUs mandan**  
  Pensar en `Security`, `Infrastructure`, `Workloads` y `Sandbox` me obliga a diseñar por **tipo de cuenta**, no por cuentas aisladas.

- **SCP como “reglas del juego”**  
  - Limitar regiones.
  - Acotar el uso de root.
  - Poner freno a Sandbox.  

  IAM concede permisos; las **SCP marcan el techo** de lo que puede pasar en cada cuenta/OU.

---

## 2. Servicios utilizados

### 2.1 AWS Organizations
- Centro de control para:
  - Crear cuentas.
  - Agruparlas en OUs.
  - Aplicar SCP a nivel de árbol.
- Diferencia clara entre **invitar cuentas** y **crearlas desde la Organization** con `OrganizationAccountAccessRole` automático.

### 2.2 IAM / IAM Identity Center
- Paso de usuarios IAM sueltos a:
  - Usuarios y grupos en Identity Center.
  - Permission sets reutilizables entre cuentas.
- Mucho más alineado con un enfoque SSO corporativo y RBAC.

### 2.3 Service Control Policies
- Muy útiles para políticas globales (regiones, root, sandbox).
- Lección importante: **probar primero en cuentas de prueba**, porque una SCP mal diseñada puede dejar una cuenta casi inutilizable.

---

## 3. Mirando a una empresa real

En un contexto corporativo tendría sentido:

- Refinar los roles más allá de “Administradores”:
  - Dev, Ops, Seguridad, Finanzas, etc.
- Integrar Identity Center con un IdP corporativo (Azure AD, Okta…) y procesos de alta/baja.
- Estandarizar **naming** y **tagging** para coste, reporting y automatización.

---

## 4. Posibles mejoras

### 4.1 AWS Control Tower
- Evaluar Control Tower como landing zone gestionada:
  - Cuentas de seguridad preconfiguradas.
  - Guardrails listos para usar.
  - Integración nativa con Organizations e Identity Center.
- Comparar este enfoque con la configuración manual del laboratorio.

### 4.2 Monitorización y seguridad

- Centralizar logs con **CloudTrail organizacional** y una cuenta `log-archive`.
- Añadir servicios como:
  - AWS Config, GuardDuty, Security Hub.
  - Alguna solución de observabilidad externa si tiene sentido.

### 4.3 Automatización e IaC

- Describir OUs, cuentas y SCP con **Terraform o CloudFormation**.
- Gestionar cambios vía GitHub (PRs + pipelines), acercándome a un modelo de **equipo de plataforma**.

---

## 5. Conclusión

Este laboratorio me ha servido para:

- Entender mejor la **gobernanza multi-cuenta en AWS**.
- Practicar con **Organizations, SCP e IAM Identity Center** sobre un caso realista.
- Empezar a pensar en términos de **guardrails y estructura**, no solo de recursos sueltos.

No es una landing zone completa, pero es una base sólida para mi portfolio y un buen punto de partida para proyectos cloud más serios.

