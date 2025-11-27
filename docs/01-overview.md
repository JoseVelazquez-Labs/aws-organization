# 01 - Overview

Este laboratorio nace con una idea concreta: **aprender a diseñar y gobernar una
AWS Organization pequeña pero seria**, y dejar ese trabajo documentado como parte
de mi portfolio.

No busco montar muchas aplicaciones, sino entender bien la “capa de organización”:
cómo estructurar cuentas, cómo empezar a poner reglas y cómo centralizar el acceso.

---

## 1. Objetivo y enfoque

De forma resumida, este proyecto me sirve para:

- Practicar el diseño de una **organización multi-cuenta** como lo haría en una empresa.  
- Empezar a aplicar **gobernanza** con un conjunto reducido de SCP.  
- Probar un modelo sencillo de **acceso centralizado** con IAM Identity Center.  
- Acostumbrarme a documentar decisiones técnicas, no solo pasos de consola.

Los detalles de la arquitectura (OUs, cuentas, diagrama, etc.) se explican en  
`02-architecture.md`. Aquí solo dejo claro **el “para qué”** del laboratorio.

---

## 2. Alcance real

Lo que **sí trabajo** en este laboratorio:

- Cómo estructurar la organización (sin entrar en automatización compleja).  
- Un primer set de SCP para marcar límites básicos.  
- Un modelo de acceso administrativo usando Identity Center.

Lo que **queda fuera por ahora**:

- IaC (Terraform / CloudFormation) para desplegar todo.  
- Control Tower u otras soluciones gestionadas.  
- Servicios de seguridad y cumplimiento avanzados.

Estas piezas se mencionan como siguientes pasos en `05-lessons-learned.md`.

---

## 3. Costes: lo que he aprendido

Aunque es un entorno de pruebas, el coste no es un detalle menor:

- **Presupuestos (AWS Budgets)**  
  Configurar alertas antes de montar la organización ayuda a detectar rápidamente
  cualquier gasto raro.

- **Créditos y free tier**  
  Al usar una cuenta con créditos como **Management Account** dentro de una
  AWS Organization, vi que los créditos dejaban de aplicarse.  
  Es algo a tener en cuenta a la hora de elegir qué cuenta usar como administración.

---

## 4. Relación con el resto de documentos

- `02-architecture.md` → diseño de la organización (cómo quiero que esté montada).  
- `03-implementation.md` → cómo la he ido construyendo paso a paso.  
- `04-current-configuration.md` → estado real a día de hoy.  
- `05-lessons-learned.md` → conclusiones y siguientes mejoras que tendría sentido abordar.
