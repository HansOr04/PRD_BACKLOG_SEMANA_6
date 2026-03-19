# PRD — Sistema de Gestión de Flota y Mantenimiento Preventivo
 
**Sofware:** FleetGuard — Gestión de Flota y Mantenimiento Automotriz  
**Fecha:** 18/03/2026
**Equipo:**
 
| Rol | Integrante | País |
|-----|-----------|------|
| QA  | Hans  Ortiz | Ecuador |
| DEV | Javier Luis | Colombia |

## Problema a resolver

El problema que se encontro en Automotive es que al gestionar su flota lo hace de forma manual y desorganizada, no tienen un mecanismo centralizado donde registre el kilometraje de cada vehículo ni que indique cuándo corresponde el mantemiento del vehiculo.
Lo que esto genera que tengan intervenciones tardías, riesgos altos de accidentes o posibles sanciones a nivel vehicular o de empresa, ademas al no hacer los mantimientos en el tiempo correcto, suben los costos de reparacion debido a la gravedad aumentada que se tiene.

## Vision
FleetGuard es un software para Automotive que sistematiza la gestion de mantenimiento de su flota vehicular. A través del registro periódico de kilometraje por vehículo, fleetguard aplica reglas de mantenimiento preventivo y alerta oportunamente cuando un vehiculo requiere intervencion, asi logramos evitar fallas, accidentes o sanciones por un descuido operativo. 

## Objetivo General
Desarrollar una aplicación que permita a Automotive gestionar de forma centralizada y preventiva el mantenimiento de su flota, mediante el seguimiento del Kilometraje y la asignación de reglas de mantenimiento según el tipo de vehículo.

## Objetivos Específicos
1. Registrar y centralizar la información de cada vehículo de la flota, para tener sistematizado de forma clara el inventario operativo.
2. Permitir el registro de forma centralizada y periodica del kilometraje recorrido por cada vehículo, garantizando que los datos ingresados sean válidos antes de ser persistidos.
3. Centralizar las reglas de mantenimiento preventivo según el tipo de vehículo, permitiendo crearlas y asociarlas con facilidad.
4. Generar alertas automáticas cuando un vehículo alcance el límite de kilometraje definido en una regla, previniendo retrasos en el mantenimiento que pueden derivar en multas o accidentes.
5. Registrar el historial de mantenimiento realizados por el vehículo, incluyendo la fecha, el kilometraje del servicio y la regla que lo originó.
6. Reducir costos opertativos con relación a los mantenimientos fuera de tiempo, por medio de intervenciones preventivas planificadas con base en datos reales. 

### 1.3 Objetivos del MVP
 
| # | Objetivo | Métrica de Éxito |
|---|---------|------------------|
| O1 | Centralizar la información de la flota | 100% de vehículos registrados |
| O2 | Registrar kilometraje periódicamente | Al menos 1 vez por semana por vehículo |
| O3 | Alertar mantenimientos antes de que se venzan | ≥ 90% ejecutados antes del umbral |
| O4 | Reducir costos por mantenimiento correctivo | -30% en reparaciones de emergencia |

## Actores del Sistema
### 2.1 Administrador de Flota
 
- **Persona:** Carlos — Jefe de Operaciones, logística (25 vehículos)
- **Dolor:** Revisa Excel para saber cuál vehículo necesita servicio. 3 fallas en carretera este año.
- **Necesidad:** Sistema que le diga qué vehículo necesita mantenimiento y cuándo.
- **Interacción:** Gestiona vehículos, reglas, alertas (consulta) e historial.
- **HU:** HU-01, HU-07, HU-09, HU-12, HU-13, HU-14, HU-16.

### 2.2 Conductor
 
- **Persona:** María — Conductora de reparto
- **Dolor:** No tiene forma rápida de reportar kilometraje de su vehículo.
- **Necesidad:** Registro rápido del kilometraje.
- **Interacción:** Solo registra kilometraje.
- **HU:** HU-04.

### 2.3 Sistema Automático (Actor No Humano)
 
- **Descripción:** Backend que valida datos, evalúa estado de mantenimiento y genera alertas sin intervención humana.
- **Interacción:** Vía scheduler o eventos post-registro.
- **HU:** HU-05, HU-06, HU-11.

## 3. Alcance del MVP
 
### 3.1 IN — 11 Historias de Usuario

| Módulo | HU | Funcionalidad | Actor |
|--------|-----|--------------|-------|
| **Vehículos** | HU-01 | Registrar vehículo con tipo asociado | Administrador |
| **Kilometraje** | HU-04 | Registrar y acumular kilometraje | Conductor |
| | HU-05 | Validar coherencia del km | Sistema |
| | HU-06 | Consultar estado de mantenimiento del vehículo | Sistema |
| **Reglas** | HU-07 | Crear regla con tipo de mantenimiento | Administrador |
| | HU-09 | Asociar regla a tipo de vehículo | Administrador |
| **Alertas** | HU-11 | Generar alerta automática por km | Sistema |
| | HU-12 | Consultar y clasificar alertas por estado | Administrador |
| **Historial** | HU-13 | Registrar mantenimiento realizado | Administrador |
| | HU-14 | Asociar mantenimiento a regla y resetear contador | Administrador |
| | HU-16 | Registrar fecha y km del servicio | Administrador |
    

### OUT — Fuera de Alcance

Funcionalidad: Consultar vehículo por placa     
Razón: Se difiere al Sprint 1, el listado general cubre la necesidad básica 

Funcionalidad: Listar vehículos disponibles     
Razón: Se difiere al Sprint 1 por alcance del MVP 

Funcionalidad: Consultar reglas         
Razón: Se difiere al Sprint 1, no bloquea el flujo de creación

Funcionalidad: Modificar reglas     
Razón: Se difiere al Sprint 1 por alcance del MVP 

Funcionalidad: Obtener historial por placa  
Razón: Trazabilidad completa para Sprint 1 

Funcionalidad: Generar alerta por tiempo        
Razón: Requiere lógica de scheduler, complejidad alta para MVP 

Funcionalidad: Eliminar reglas  
Razón: Riesgo de perder trazabilidad de mantenimientos anteriores 

Funcionalidad: Deshabilitar / habilitar vehículo    
Razón: No bloquea el flujo preventivo del MVP 

Funcionalidad: Modificar vehículo por placa     
Razón: MVP prioriza el registro inicial, los datos no son editables por ahora

Funcionalidad: GPS / Telemetría     
Razón: Requiere integración con hardware externo

Funcionalidad: Gestión de combustible   
Razón: Módulo independiente, no relacionado al mantenimiento preventivo

Funcionalidad: App móvil nativa     
Razón: MVP opera como aplicación web 

Funcionalidad: Integración ERP  
Razón: Depende de adaptadores específicos por cliente 

Funcionalidad: SMS / WhatsApp   
Razón: MVP no contempla notificaciones externas 

Funcionalidad: Reportes PDF     
Razón: Funcionalidad de valor agregado para versiones futuras 

Funcionalidad: Multi-tenancy    
Razón: MVP opera para una sola organización 


## Riesgos

### Riesgos de Negocio

- Conductores no registran el kilometraje     
Probabilidad: Alta   
Impacto: Alto   
Si los conductores no reportan el km recorrido de forma periódica, el sistema no tiene datos reales para evaluar cuándo generar una alerta, la aplicación no cumpliría su función.  
Mitigación: El flujo de registro debe ser simple y tomar menos de 30 segundos.

- Reglas que no reflejan la realidad operativa        
Probabilidad: Media     
Impacto: Medio  
Si el administrador configura reglas con intervalos incorrectos, el sistema generará alertas equivocados, perdiendo confianza el producto.   
Mitigación: Las reglas deben ser flexibles y editables por tipo de vehículo.

- Administrador ignora las alertas generadas      
Probabilidad: Media     
Impacto: Alto   
Si las alertas no se atienden, el sistema funciona correctamente pero el mantenimiento igual se retrasa, que es exactamente el problema que se quiere resolver.     
Mitigación: Las alertas deben tener escalamiento visual según su nivel de urgencia.

- Resistencia al cambio por parte del equipo      
Probabilidad: Alta      
Impacto: Medio     
Al pasar de un proceso manual a uno sistematizado, es probable que conductores y administradores no adopten el sistema de inmediato.        
Mitigación: Acompañamiento en la migración inicial y capacitación básica de uso.

### Riesgos tecnicos

- Datos inconsistentes de km   
Probabilidad: Alta      
Impacto: Alto
Se tiene que tener en cuenta la importancia de las comas o los puntos al momento de colocar el kilometraje, por que en algunas localidades suele variar al contar decimales o al contar miles        
Mitigación:
Necesitamos realizar una validacion estricta que coloque directamente el formato sin que el usuario se preocupe por ello


- Planificador lento con flotas grandes   
Probabilidad: Bajo      
Impacto: Alto
Al momento de realizar una consulta o una alerta si no se realiza una optimizacion de la informacion con normalizacion de la base de datos, se podria generar un flujo demasiado lento lo que podria ocasionar fallos      
Mitigación:
Se debe realizar querys optimizadas y una evaluacion incremental para estar optimizando cada vez las mismas

- Pérdida de datos  
Probabilidad: Bajo      
Impacto: Critico
Si en algun momento la base de datos se ve comprometida por medidas de seguridad o por ejecuciones de Querys no debidas.       
Mitigación:
BackUps diarios debido a la perseveracion de los datos y la integridad de los mismos

- Vulnerabilidades API 
Probabilidad: Media      
Impacto: Critico
Situaciones donde la informacion se puede filtrar o visualizar porque no hay un medio de seguridad aplicado  
Mitigación:
Generacion de tokens, roles o validacion de la informacion en inputs para evitar riesgos de seguridad

- Alertas falsas por reglas mal configuradas
Probabilidad: Media      
Impacto: Alto
Al generar una alerta por no realizar bien el analisis de lo que se necesita puede dar falsos positivos  
Mitigación:
Es un testing exhaustivo con las reglas de negocio para poder validar cada una de estas, ademas de una UI intuitiva que no genere ambiguedad al momento de colocar la regla

- Race conditions en registro concurrente km
Probabilidad: Bajo      
Impacto: Medio
Al momento de registrar un valor de km si dos condutores mandan al mismo tiempo un valor esto puede perjudicar al registro de cada vehiculo
Mitigación:
Se realiza un locking Optimista con el cual si dos usuarios mandan un dato al mismo vehiculo a la vez guarda el primero que entro y despues ingresa nuevamente el otro valor y lo suma al nuevo valor


## Riesgos QA

Riesgo: Cobertura insuficiente del motor de reglas
Mitigacion: Matriz: km variados, umbrales exactos, sin reglas

Riesgo: Escenarios de borde en validación km 
Mitigacion:Probar: igual, negativo, excesivo

Riesgo:Alertas duplicadas o perdidas 
Mitigacion:Pruebas de idempotencia del scheduler

Riesgo:Reseteo incorrecto del contador 
Mitigacion:Verificar cálculo con datos controlados

Riesgo:Clasificación incorrecta de alertas
Mitigacion:Probar flujo de transiciones PENDING→WARNING→OVERDUE→RESOLVED

Riesgo:Evaluación de estado inconsistente
Mitigacion:Probar: al día, próximo, vencido, sin reglas, múltiples reglas