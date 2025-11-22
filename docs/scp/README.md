# Service Control Policies (SCP)

En esta carpeta guardo las **Service Control Policies** que utilizo en el laboratorio de la organización de AWS.

Cada fichero `.json` contiene la definición completa de una SCP.  
En el documento `04-configuration.md` explico con más detalle **cómo se aplican** estas políticas sobre las OUs y las cuentas.

## Políticas incluidas

| Fichero                                       | Descripción rápida                                                        |
|----------------------------------------------|---------------------------------------------------------------------------|
| `SCP-Restrict-Regions.json`                  | Limita el uso de la organización a un conjunto de regiones permitidas.   |
| `SCP-Restrict-Root.json`                     | Restringe el uso del usuario root para tareas muy concretas.             |
| `SCP-Protect-CloudTrail.json`                | Protege CloudTrail (evita paradas, borrados o modificaciones críticas).  |
| `SCP-Limit-Sandbox-Costs.json`               | Aplica restricciones adicionales en la OU Sandbox para controlar costes. |

## Notas importantes

- Estas políticas están pensadas como **ejemplos de laboratorio** para mi proyecto personal.
- No están revisadas para entornos de producción reales.
- Antes de usarlas en una empresa habría que:
  - Revisar acciones, recursos y condiciones.
  - Adaptarlas a los requisitos de seguridad, cumplimiento y costes de esa organización.

Para entender el contexto completo (arquitectura, OUs y cuentas) se recomienda leer:

- `../02-architecture.md`
- `../04-configuration.md`
