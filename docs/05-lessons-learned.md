# 05 - Lessons learned

Resumen de lo más importante que me llevo de este laboratorio.

---

## 1. Gobernanza

- **Multi-cuenta desde el inicio**  
  Separar Prod / Dev / Sandbox en cuentas distintas hace más fácil:
  - Delegar permisos.
  - Auditar.
  - Controlar costes.

- **Diseñar por tipo de cuenta, no por cuenta suelta**  
  OUs como `Security`, `Infrastructure`, `Workloads`, `Sandbox` ayudan a pensar en:
  - Qué va en cada “bloque”.
  - Qué reglas (SCP) aplican a cada uno.

- **SCP como marco de seguridad**  
  - Límite de regiones.
  - Root muy controlado.
  - Sandbox con freno de costes.  

  IAM otorga permisos; las SCP marcan el perímetro.

---

## 2. Servicios clave

- **AWS Organizations**  
  - Crear cuentas, agruparlas en OUs y aplicar SCP.
  - Diferencia clara entre invitar cuentas y crearlas desde la Organization.

- **IAM / IAM Identity Center**  
  - De usuarios IAM individuales a:
    - Usuarios + grupos en Identity Center.
    - Permission sets reutilizables por cuenta.
  - Más parecido a un SSO corporativo real.

- **Service Control Policies**  
  - Potentes para reglas globales.  
  - Lección: probar siempre primero en Sandbox para no bloquear una cuenta por error.

---

## 3. Enfoque “empresa real”

Si esto creciera en producción, tendría sentido:

- Más roles y grupos (Dev, Ops, Seguridad, Finanzas…).  
- Integración de Identity Center con directorio corporativo.  
- Naming y tagging consistentes para coste y automatización.

---

## 4. Próximos pasos

- Valorar **AWS Control Tower** como landing zone gestionada.  
- Añadir capa de seguridad y observabilidad:
  - CloudTrail organizacional + `log-archive`.
  - AWS Config, GuardDuty, Security Hub, etc.
- Pasar la organización a **IaC** (Terraform / CloudFormation) y gestionarla vía GitHub.

---

## 5. Cierre

Este laboratorio me ha servido para:

- Entender mejor la **gobernanza multi-cuenta**.
- Practicar con **Organizations, SCP e Identity Center** en un caso coherente.
- Tener una base realista que puedo enseñar en el portfolio y seguir ampliando.

