# FleetGuard — Gestión de Flota y Mantenimiento Preventivo

## Descripcion del proyecto

Este repositorio contiene un sistema para registrar kilometraje vehicular y alertar mantenimientos preventivos basados en reglas asignadas por tipo de vehículo.

## Equipo 

Rol QA | Hans Alazar Ortiz Bailon | Ecuador     
Rol Dev | Javier Luis | Colombia

## Análisis de User Stories

Realizamos un **User Story Mapping** en Miro para analizar todas las historias de usuario que podrían ser relevantes para el sistema. En total identificamos **16 HU**, pero tras un análisis de tiempos y priorización las redujimos a **11 HU para el MVP**.

En el tablero se pueden ver las historias seleccionadas, las descartadas y el contexto y criterios de aceptación que logramos extraer de cada una.

🔗 [Ver tablero Miro — User Story Mapping](https://miro.com/app/board/uXjVGwrNHh0=/?share_link_id=821967920981)

## Actores 

| Actor | HU |
|-------|----|
| Administrador de flota | HU-01, HU-06, HU-07, HU-09, HU-12, HU-13, HU-14, HU-16 |
| Conductor | HU-04, HU-06, HU-12 |
| Sistema automático | HU-05, HU-11 |

## Entregables 

| Archivo | Descripción |
|-------|----|
| PRD.md | Visión, alcance MVP, riesgos |
| USER_STORIES.md | 11 HU + Gherkin = Story Points |
| SUBTASKS.md | Tasking DEV + QA por HU |
| [Tablero Github Projects](https://github.com/users/HansOr04/projects/1) | Board público |

## MVP - 11 HU / 32 SP

| Módulo | HU | SP |
|--------|----|----|
| Vehículos | HU-01 | 8 |
| Kilometraje | HU-04, HU-05, HU-06 | 16 |
| Reglas | HU-07, HU-09 | 10 |
| Alertas | HU-11, HU-12 | 16 |
| Historial | HU-13, HU-14, HU-16 | 18 |

## Convenciones de Commits 
```
feat(prd): add vision and objectives
feat(backlog): add HU-01 vehicle registration
feat(gherkin): add scenarios for HU-05
docs(tasking): add subtasks for HU-11
```