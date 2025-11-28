# AWS Multi-account Organization Lab

Laboratorio personal donde diseño y documento una **AWS Organization multi-cuenta**  
pensando en un entorno “tipo empresa”, pero de tamaño controlado y asumible para un perfil junior.

El foco está en **gobernanza y acceso**, no en desplegar aplicaciones complejas.

Tecnologías principales:

- **AWS Organizations** → OUs, cuentas miembro y estructura multi-cuenta.
- **IAM + Service Control Policies (SCP)** → reglas globales de seguridad y uso.
- **IAM Identity Center (SSO)** → acceso centralizado por grupos y permission sets.

No es una landing zone corporativa completa, pero sí una **base seria y realista**
para practicar buenas prácticas de organización en AWS.

---

## Diagrama principal

![Diagrama de la organización AWS](./docs/screenshots/02-aws_organization_architecture.png)

> El diseño completo (cuentas, OUs y principios) se explica en  
> `docs/02-architecture.md`.

---

## Contenido del repositorio

```text
aws-organization/
├── docs/
│   ├── 01-overview.md              # Contexto general y objetivos del laboratorio
│   ├── 02-architecture.md          # Diseño objetivo: cuentas, OUs y decisiones de arquitectura
│   ├── 03-implementation.md        # How-to paso a paso (Organizations, SCP, Identity Center)
│   ├── 04-current-configuration.md # Foto real de lo que está desplegado hoy
│   └── 05-lessons-learned.md       # Aprendizajes y siguientes pasos posibles
│
├── docs/scp/                       # Service Control Policies en formato JSON
│   ├── SCP-Restrict-Regions.json
│   ├── SCP-Restrict-Root.json
│   ├── SCP-Protect-CloudTrail.json
│   └── SCP-Limit-Sandbox-Costs.json
│
└── docs/screenshots/               # Capturas de consola y diagramas usados en la doc

````
## Nota sobre costes

Aunque es un entorno de laboratorio, hay dos puntos importantes:

- **AWS Budgets**: Es muy recomendable crear uno o varios presupuestos con alertas (por ejemplo, al 50 %, 80 % y 100 %)
antes de montar la Organization, para detectar cualquier gasto inesperado.

- **Créditos y free tier**:
Algunas cuentas nuevas incluyen créditos promocionales (por ejemplo, 200 USD).
En mi caso, al usar esa cuenta como Management Account dentro de una AWS Organization,
esos créditos dejaron de aplicarse.
Por eso lo reflejo en la documentación: en un entorno real puede influir en qué cuenta eliges
como cuenta de administración.
