# USER_STORIES.md — Historias de Usuario FleetGuard

## HU-01: Registrar vehículo con tipo asociado

**Como** administrador de flota     
**Quiero** registrar un nuevo vehículo indicando su placa, marca, modelo, tipo combustible y tipo de vehículo       
**Para** centralizar mi flota con la clasificación necesaria para aplicar reglas de mantenimiento.

## HU-04 Registrar y acumular kilometraje

**Como** conductor     
**Quiero** registrar el kilometraje marcado en el odómetro de mi vehículo al terminar mi recorrido      
**Para** que el sistema mantenga el acumulado al día y pueda detectar cuando requiere mantenimiento.

## HU-05 Validar coherencia del kilometraje

**Como** sistema     
**Quiero** validar la coherencia del kilometraje ingresado por el conductor      
**Para** evitar que datos incorrectos dañen el acumulado del vehículo y provoquen alertas equivocadas

## HU-06 Consultar estado de mantenimiento del vehículo

**Como** administrador de flota o conductor     
**Quiero** consultar si existen mantenimientos pendientes del vehículo      
**Para** tomar acciones oportunas antes de que se venza el límite definido en la regla

## HU-07 Crear regla con tipo de mantenimiento

**Como** administrador de flota     
**Quiero** crear una regla de mantenimiento preventivo indicando el tipo de mantenimiento y el intervalo de kilometros en que debe realizarse      
**Para** tener definidas las condiciones bajo las cuales el sistema debe alertar que un vehículo requiere intervención 

## HU-09 Asociar regla a tipo de vehículo

**Como** administrador de flota     
**Quiero** asociar un regla de mantenimiento a un tipo de vehículo      
**Para** que el sistema sepa qué condiciones de mantenimiento aplican a cada vehículo de la flota 

## HU-11 Generar alerta automática por kilometraje

**Como** sistema     
**Quiero** generar una alerta automática cuando el kilometraje total de un vehículo alcance el límite definido de una regla      
**Para** notificar oportunamente que ese vehículo requiere mantenimiento

## HU-12 Consultar y clasificar alertas

**Como** administrador de flota o conductor     
**Quiero** consultar las alertas activas que tiene un vehículo y poderlas clasificar según su estado      
**Para** priorizar los mantenimientos más urgentes y mantener el vehículo al día y en operación

## HU-13 Registrar mantenimiento

**Como** administrador de flota
**Quiero** registrar que un mantenimiento fue realizado sobre un vehículo
**Para** dejar constancia del mantenimiento y cerrar la alerta generada