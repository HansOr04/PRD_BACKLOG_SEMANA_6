PRD-Sistema de Gestión de Flota y Mantenimiento preventivo
Caso de Producto: Automotive- Gestor de flota y Mantenimiento Automotriz
Fecha de inicio: 18/03/2026
Equipo y rol
Javier Luis DEV
Hans Ortiz QA

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

## Métrica de Éxito
- El 100% de los vehículos registrados deben tener como mínimo placa, modelo y tipo asociado.
- Cada vehículo debe reportar su kilometraje, máximo 48 horas luego de efectuarse el recorrido.
- La mayoría de los mantenimientos deben hacerse antes de que el vehículo llegue al límite definido, generar una efectividad de un 90% antes de tiempo.
- Reducir en un 30% los daños y accidentes inesperados gracias al mantenimiento preventivo.

## Actores del Sistema

### Administrador de Flota 
Persona: Carlos - Jefe de operaciones, logistica (25 vehículos)
Dolor: Revisa excel para saber cuál vehículo necesita servicio. 3 fallas en carretera este año.
Necesidad: Sistema que le diga qué vehículo necesita mantenimiento y cuándo.
HU relacionadas: HU-01, HU-07, HU-09, HU-13, HU-14.

### Conductor
Persona: María' Condutora de reparto
Dolor: No puede reportar de forma rapida el kilometraje de su vehiculo
Necesidad: Registro rapido del kilometraje
HU relacionadas: HU-04

### Sistema Automatico
Descripcion: Sistema backend que valida datos y genera alertas sin intervención humana.
HU relacionadas: HU-05, HU-11.

### Alcance MVP

### IN- 11 Historias de Usuario

Modulo Vehiculos    
Historia de Usuario: HU-01  
Funcionalidad: Registrar vehiculo con tipo asociado     
Actor: Administrador de Flota

Modulo Kilometraje  
Historia de Usuario: HU-04  
Funcionalidad: Registrar y acumular kilometraje     
Actor: Conductor

Modulo Kilometraje  
Historia de Usuario: HU-05  
Funcionalidad: validar coherencia del km    
Actor: Sistema

Modulo Kilometraje  
Historia de Usuario: HU-06  
Funcionalidad: Consultar estado de mantenimiento del vehículo   
Actor: Sistema

Modulo Reglas   
Historia de Usuario: HU-07  
Funcionalidad: Crear regla con tipo de mantenimiento    
Actor: Administrador de Flota

Modulo Reglas   
Historia de Usuario: HU-09  
Funcionalidad: Asociar regla a tipo de vehículo     
Actor: Administrador de Flota

Modulo Alertas  
Historia de Usuario: HU-11  
Funcionalidad: Generar alerta automática por km     
Actor: Sistema

Modulo Alertas  
Historia de Usuario: HU-12  
Funcionalidad: Consultar y clasificar alertas por estado    
Actor: Administrador de Flota

Modulo Historial    
Historia de Usuario: HU-13  
Funcionalidad: Registrar mantenimiento realizado    
Actor: Administrador de Flota

Modulo Historial    
Historia de Usuario: HU-14  
Funcionalidad: Asociar mantenimiento a regla y resetear contador    
Actor: Administrador de Flota

Modulo Historial    
Historia de Usuario: HU-16  
Funcionalidad: Registrar fecha y km del servicio    
Actor: Administrador de Flota 

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