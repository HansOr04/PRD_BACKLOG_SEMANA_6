# REALITY_CHECK.md — FleetGuard MVP

**Proyecto:** FleetGuard — Sistema de Gestión de Flota y Mantenimiento Preventivo
**Versión:** 1.1
**Fecha de cierre:** 08 de abril de 2026
**QA:** Hans Ortiz | **DEV:** Javier Luis
**Programa:** Sofka Technologies — Training IA Native — Semana 7

> Este documento es el análisis retrospectivo honesto del equipo al finalizar el micro-sprint. Responde las tres preguntas clave del taller: qué subestimamos, si el MVP tiene valor real y cómo se garantizó la calidad.

---

## 1. ¿QUÉ TAREAS SUBESTIMAMOS Y POR QUÉ?

### 1.1 Estado final del sprint

De las 11 HUs del backlog inicial, el equipo implementó y entregó **7 historias**, que corresponden exactamente al núcleo funcional del MVP priorizado desde el inicio:

| HU | Descripción | Estado DEV | Estado QA |
|---|---|---|---|
| HU-01 | Registrar vehículo con tipo asociado | ✅ Completo | ✅ Cubierto en 3 niveles |
| HU-04 | Registrar y acumular kilometraje | ✅ Completo | ✅ Cubierto en 3 niveles |
| HU-05 | Validar coherencia del km | ✅ Completo | ✅ Cubierto en 3 niveles |
| HU-07 | Crear regla con tipo de mantenimiento | ✅ Completo | ✅ Cubierto en 3 niveles |
| HU-09 | Asociar regla a tipo de vehículo | ✅ Completo | ✅ Cubierto en 3 niveles |
| HU-11 | Generar alerta automática por km | ✅ Completo | ✅ Cubierto (parcial) |
| HU-13 | Registrar mantenimiento realizado | ✅ Completo | ✅ Cubierto en 3 niveles |
| HU-06 | Consultar estado de mantenimiento | ⏳ Diferida | ⏳ Documentada en GAP REPORT |
| HU-12 | Consultar y clasificar alertas | ⏳ Diferida | ⏳ Documentada en GAP REPORT |
| HU-14 | Asociar mantenimiento a regla y resetear | ⏳ Diferida | ⏳ Documentada en GAP REPORT |
| HU-16 | Registrar fecha y km del servicio | ⏳ Diferida | ⏳ Documentada en GAP REPORT |

Las 4 HUs diferidas no representan un fracaso del sprint sino una **decisión estratégica consciente**: el equipo priorizó entregar las funcionalidades que conforman el flujo completo del negocio (registro → km → reglas → alertas → mantenimiento) sobre entregar más HUs incompletas.

### 1.2 Tarea más subestimada: setup del entorno

**Lo estimado:** Configuración de Java + Docker Compose en 1-2 horas (tarea trivial al inicio del sprint).

**Lo real:** Casi un día completo de trabajo. Tuvimos que **bajar la versión de Java** a JDK 17 porque el código fuente original asumía una versión que no era la disponible en mi máquina, y **ajustar el Docker Compose** porque las versiones iniciales de las imágenes de PostgreSQL y RabbitMQ tenían incompatibilidades con el host. Lo que parecía una tarea de "levantar contenedores" terminó siendo un debugging de versiones cruzadas entre Java, Spring Boot, Maven, Docker, y los plugins de cada uno.

### 1.3 Lo subestimado en la fase de QA

**Curva de aprendizaje de Karate y k6:** Ninguno de los dos lo había usado en producción antes. Karate tiene una sintaxis Gherkin extendida con su propio DSL, y k6 usa JavaScript con su propio modelo de ejecución (VUs, stages, thresholds). Lo que pensé que sería "leer la doc y arrancar" fue en realidad varias horas de prueba y error: aprender cómo manejar variables de scenario, cómo hacer setup compartido entre features, cómo configurar tags, cómo medir métricas custom en k6, cómo hacer polling de eventos asíncronos.

**Identificación de gaps del backend:** Esta fue probablemente la tarea más subestimada de todas. Cuando empecé a escribir los tests Karate basándome en los Criterios de Aceptación de las HUs, asumí que el backend implementaba todo lo que las HUs decían. Tuve que hacer una **revisión cruzada manual entre cada CA y el código fuente real** de los Controllers, DTOs y Services para descubrir que **13 escenarios definidos en las HUs no eran ejecutables** porque el backend nunca los implementó dentro del scope del MVP. Esto no fue tiempo perdido — al contrario, generó el GAP REPORT que es uno de los entregables más valiosos del sprint — pero ningún Story Point lo contemplaba.

### 1.4 Lo NO subestimado

**Las pruebas unitarias.** Esta parte fluyó sorprendentemente bien. Una vez configurado el entorno y entendida la arquitectura hexagonal del backend, generar los 151 tests unitarios fue cuestión de aplicar patrones consistentes: sin mocks en Domain, Mockito en Application, `@WebMvcTest` en Controllers. La cobertura del 76% en fleet-service y ≥70% en rules-alerts-service se alcanzó sin ajustar el alcance.

**El refactor del backend (3 controllers).** A mitad del sprint, el DEV partner refactorizó `VehicleController` en 3 controllers separados (`VehicleController`, `MileageController`, `VehicleQueryController`) por principio de responsabilidad única. Pensé que iba a romper todos los tests Karate, pero como los paths permanecieron idénticos, no hubo impacto. Esa decisión arquitectónica fue acertada y bien comunicada.

### 1.5 Tabla de tiempo estimado vs real

| Tarea | Estimado | Real | Desvío |
|---|---|---|---|
| Setup de entorno (Java, Docker, BD, Rabbit) | 2h | ~6h | +200% |
| 151 pruebas unitarias en 2 servicios | 12h | ~14h | +17% |
| Suite Karate inicial (6 escenarios) | 6h | ~7h | +17% |
| Suite Karate ampliada (8 nuevos escenarios) | — | ~3h | nuevo |
| Identificación de gaps del backend (revisión cruzada CA ↔ código) | 0h (no estimado) | ~4h | +∞% |
| Suite Serenity E2E (8 escenarios) | 8h | ~10h | +25% |
| Extracción de selectores frontend | 1h | ~2h | +100% |
| Test Plan, Test Cases, Reality Check | 4h | ~5h | +25% |
| Gap Report formal | 0h (no estimado) | ~2h | +∞% |

**Total estimado:** 33 horas. **Total real:** ~53 horas. **Desvío del sprint: +60%.**

### 1.6 Lecciones aprendidas

1. **Los Story Points estiman la funcionalidad, no la fricción.** Las tareas de setup, configuración y resolución de incompatibilidades son invisibles al estimar pero consumen tiempo real.
2. **No todo lo que dicen las HUs está en el código.** Hacer una revisión cruzada manual entre Criterios de Aceptación y código real es un trabajo formal de QA que debería tener su propio tiempo asignado en futuros sprints.
3. **Las herramientas nuevas siempre toman más tiempo del esperado.** Karate y k6 son potentes pero tienen su curva. Asignar tiempo de "spike" o investigación previa al sprint formal hubiera ayudado.
4. **El refactor bien comunicado no rompe nada.** Cuando Javier separó VehicleController en 3, me avisó con anticipación y mantuvo los paths estables. No tuve que reescribir un solo test.

---

## 2. ¿EL MVP TIENE VALOR REAL PARA EL NEGOCIO?

**Sí, el MVP tiene valor real medible para el negocio de gestión de flotas automotrices.**

### 2.1 Capacidades entregadas vs estado anterior

| Capacidad | Antes (proceso manual) | Con FleetGuard MVP |
|---|---|---|
| Inventario de vehículos | Hojas de Excel descentralizadas | Base de datos centralizada con tipos asociados |
| Registro de kilometraje | Cuadernos de bitácora del conductor | API + frontend con validación de coherencia |
| Reglas de mantenimiento por tipo de vehículo | Conocimiento tácito del mecánico | Reglas configurables centralizadas |
| Detección de mantenimiento próximo | Inspección visual del odómetro | Alertas automáticas vía RabbitMQ |
| Historial de servicios | Hojas sueltas o sistemas separados | Registro digital con trazabilidad |

### 2.2 El flujo crítico funciona end-to-end

El equipo validó el flujo completo manualmente y mediante 3 niveles de pruebas automatizadas:

1. **Operador registra un vehículo** con tipo "Sedán" → backend guarda en `fleet_db`
2. **Administrador crea regla** "Cambio de aceite cada 10000 km con umbral de 500 km"
3. **Administrador asocia la regla** al tipo "Sedán"
4. **Conductor reporta km = 9500** → fleet-service publica `MileageRegistered` en RabbitMQ
5. **rules-alerts-service consume el evento** → evalúa reglas → genera alerta `PENDING`
6. **Administrador consulta alertas activas** del vehículo → ve la alerta con `ruleName` enriquecido
7. **Administrador registra el servicio realizado** → la alerta cambia a `RESOLVED`

Este flujo de 7 pasos atraviesa los 2 microservicios, RabbitMQ, las 2 bases de datos, y el frontend Next.js. **Funciona sin errores.** Es lo que valida que el MVP tiene valor: no son funciones aisladas, es un sistema operativo de extremo a extremo.

### 2.3 ¿Por qué las 4 HUs diferidas no bloquean el valor?

- **HU-06 (consultar estado):** Las alertas asíncronas de HU-11 ya notifican al usuario cuando hay mantenimiento próximo. La consulta activa es complementaria, no crítica.
- **HU-12 (clasificar PENDING/WARNING/OVERDUE):** El sistema actual genera alertas en estado PENDING. La clasificación granular es nice-to-have para priorizar visualmente, pero no impide que el administrador vea las alertas.
- **HU-14 (recálculo del próximo servicio):** Esta es la HU diferida más impactante porque limita el sistema al primer ciclo de mantenimiento. Sin embargo, para validar el MVP del concepto, el primer ciclo es suficiente. Es la primera HU que se priorizará en la siguiente iteración.
- **HU-16 (validación de coherencia del km al servicio):** Sin esta validación, el sistema acepta datos potencialmente incoherentes. Es una mejora de robustez, no un bloqueo del flujo.

### 2.4 Bugs encontrados durante el sprint

**0 bugs bloqueantes o críticos.** El compilable corre, los flujos funcionan, los tests pasan. Lo que se encontró fue **deuda técnica documentada** (los 13 escenarios del GAP REPORT), no defectos.

### 2.5 ¿Compila y corre el software entregado?

Sí. El comando `docker-compose up -d` levanta los 5 contenedores (`fleet-db`, `rules-alerts-db`, `rabbitmq`, `fleet-service`, `rules-alerts-service`) sin errores. El frontend con `npm run dev` arranca correctamente. Las 173 pruebas automatizadas (151 unitarias + 14 Karate + 8 E2E) ejecutan sobre el sistema real y pasan al 100%.

---

## 3. ¿CÓMO GARANTIZAMOS LA CALIDAD EN TAN POCO TIEMPO?

**Honestidad primero: no se pudo asegurar la calidad al 100%.** Es imposible en un micro-sprint. Pero se tomó una serie de decisiones estratégicas que garantizan que la funcionalidad principal del sistema tiene una calidad demostrable y auditable, suficiente para un MVP entregable.

### 3.1 Decisión 1 — Pirámide de testing real, no aspiracional

En lugar de intentar "hacer un poco de todo", se construyó una pirámide de testing con responsabilidades claras por nivel:

```
       ▲
       │   8 E2E (Serenity BDD)        ← Flujo completo del usuario
       │   14 API (Karate DSL)          ← Contratos HTTP de los endpoints
       │   151 Unitarias (JUnit 5)      ← Lógica interna y reglas de dominio
       ▼
```

Cada nivel se ejecuta donde tiene sentido (unitarias en el mismo repo del producto; Karate y Serenity en repos independientes), con las herramientas correctas para su capa, y cubriendo lo que ese nivel puede cubrir mejor que los otros.

**Resultado:** 173 tests automatizados, 0 fallos, 76% de cobertura unitaria en fleet-service.

### 3.2 Decisión 2 — Priorización por criticidad, no por cobertura ciega

Antes de empezar a escribir pruebas, identifiqué los **5 casos críticos** que tenían que pasar sí o sí:

1. **Best-effort publish:** un fallo en RabbitMQ no debe romper el registro de km
2. **Idempotencia de alertas:** no duplicar alertas PENDING
3. **Skip de vehículos INACTIVE:** no evaluar reglas para vehículos inactivos
4. **Validación de recordedBy obligatorio:** evitar registros de km huérfanos
5. **Resolución de alertas:** garantizar que registrar un servicio cierra la alerta

Estos 5 casos se cubrieron primero, en pruebas unitarias detalladas, **antes** de pensar en otros escenarios. Si el sprint hubiera fallado a mitad de camino, al menos estos 5 estarían cubiertos.

### 3.3 Decisión 3 — Documentar lo que NO se prueba

Esta fue la decisión más importante para mi rol como QA. En lugar de fingir que todo está cubierto, documenté formalmente los 13 escenarios de las HUs que **no son ejecutables hoy** porque el backend no implementó las funcionalidades requeridas. Esto se entrega al equipo DEV como **GAP REPORT** (sección 13 del TEST_PLAN.md) con:

- HU afectada
- Endpoint o lógica faltante
- Response esperado según los Criterios de Aceptación
- Cantidad de escenarios bloqueados
- Impacto al negocio

Reportar lo que falta es trabajo de QA tan importante como escribir lo que pasa.

### 3.4 Decisión 4 — Revisión cruzada manual entre CA y código

Cuando comencé a escribir los tests de Karate, me di cuenta de que los Criterios de Aceptación de las HUs no siempre coincidían con lo que el backend realmente implementaba. En lugar de escribir tests basándome solo en las HUs (que iban a fallar) o solo en el código (que iba a perder cobertura de los CA), hice una **revisión manual cruzada** archivo por archivo:

- Leí cada Controller del backend para conocer los endpoints reales
- Leí cada DTO para conocer los campos exactos y las validaciones
- Leí cada Service para conocer la lógica de negocio implementada
- Crucé esa información contra los CA de cada HU
- Marqué cada escenario como "Implementable" o "Gap del backend"

Este trabajo manual de ~4 horas generó el GAP REPORT y permitió escribir 8 nuevos escenarios Karate que cubrían exactamente lo que faltaba en la versión inicial.

### 3.5 Decisión 5 — Pruebas exploratorias en los puntos de mayor riesgo

Además de las pruebas automatizadas, hice pruebas exploratorias manuales sobre los puntos del sistema que tenían más complejidad o riesgo:

- **Comunicación asíncrona RabbitMQ:** validé manualmente con la consola de management de RabbitMQ que el evento `MileageRegistered` se publica al exchange correcto (`fleetguard.exchange`) con la routing key correcta (`mileage.registered`) y que el consumer del rules-alerts-service efectivamente lo recibe. Esto NO se puede validar 100% con tests unitarios.
- **Frontend `/services` flujo de 2 pasos:** la página tiene un flujo donde primero consultas alertas, y solo después aparecen los campos del formulario. Validé manualmente que los elementos condicionales aparecen y desaparecen correctamente, y documenté esa secuencia en `SELECTORS.md`.
- **Toast del frontend:** dura 4 segundos exactos, validé manualmente con cronómetro y verifiqué que `WaitUntil` de Serenity con timeout de 5 segundos lo detecta consistentemente.

### 3.6 Tabla de decisiones de calidad y su impacto

| Decisión | Beneficio inmediato | Beneficio a largo plazo |
|---|---|---|
| Pirámide de testing real | 173 tests automatizados pasando | Base para CI/CD futuro |
| Priorización por criticidad | 5 casos críticos cubiertos primero | Reduce riesgo de regresiones en lo crítico |
| Documentar lo que no se prueba (GAP REPORT) | Transparencia con el equipo DEV | Backlog priorizado para iteraciones futuras |
| Revisión cruzada CA ↔ código | 8 nuevos escenarios Karate descubiertos | Detección temprana de inconsistencias |
| Pruebas exploratorias en puntos de riesgo | Validación de RabbitMQ, UI condicional, timing del toast | Documentación viva de cómo funciona el sistema |

---

## RESUMEN EJECUTIVO

| Pregunta del taller | Respuesta |
|---|---|
| ¿Qué subestimamos? | El setup del entorno (versiones de Java, Docker), la curva de Karate y k6, y especialmente el tiempo de hacer la revisión cruzada entre CA y código real para identificar gaps del backend |
| ¿Por qué lo subestimamos? | Los Story Points estiman funcionalidad pero no contemplan configuración de entorno ni curva de aprendizaje. La revisión cruzada CA↔código no era parte del proceso formal del sprint |
| ¿Algún bug o comportamiento inesperado? | 0 bugs bloqueantes. 13 escenarios de las HUs no son testeables porque el backend no implementó las funcionalidades — esto se reporta como GAP REPORT formal, no como bugs |
| ¿Algo que tuvimos que investigar? | QA: cómo usar Karate DSL, cómo medir métricas custom en k6, cómo extraer selectores estables del DOM Next.js + Tailwind. DEV: ninguna investigación crítica |
| ¿Compila y corre? | Sí. `docker-compose up -d` levanta todo, frontend arranca, 173 tests pasan al 100% |
| ¿El MVP tiene valor real? | Sí. El flujo completo registro → km → reglas → alertas → mantenimiento funciona de extremo a extremo y resuelve un problema real de gestión de flotas que antes se hacía manualmente |
| ¿Cómo garantizamos calidad? | Pirámide de testing real (151 unitarias + 14 Karate + 8 E2E), priorización por criticidad de los 5 casos más importantes, documentación honesta de los 13 escenarios no testeables (GAP REPORT), revisión cruzada manual entre CA y código, y pruebas exploratorias en puntos de mayor riesgo |
| ¿Cobertura alcanzada? | 76% en fleet-service (objetivo era 70%), ≥70% en rules-alerts-service. 0 fallos en 173 tests automatizados |

---

## CONCLUSIÓN

En un micro-sprint, la calidad no se garantiza con cobertura del 100%. Se garantiza con **decisiones inteligentes sobre qué probar primero**, **documentación que permita reproducir y escalar las pruebas**, y **honestidad sobre lo que quedó pendiente**.

El MVP entregado tiene una calidad funcional adecuada para su propósito: demostrar que el flujo preventivo de mantenimiento de una flota automotriz puede sistematizarse y funciona correctamente en sus casos más críticos. Las funcionalidades no implementadas no son fallos del sprint sino una decisión consciente de priorizar el flujo core sobre la completitud, y están formalmente documentadas como deuda técnica priorizada para iteraciones futuras.

El trabajo de QA en este sprint no se limitó a "escribir tests que pasaran". Incluyó identificar y reportar 13 funcionalidades que las HUs definían pero que el backend no implementó — un hallazgo que solo es posible cuando el rol de QA no se limita a validar lo que existe, sino que también compara lo que existe contra lo que las especificaciones dicen que debería existir.

---

*FleetGuard MVP · Sofka Technologies Training IA Native · Reality Check v1.1 · 2026*
*Elaborado por Hans Ortiz (QA Engineer) y Javier Luis (DEV)*
