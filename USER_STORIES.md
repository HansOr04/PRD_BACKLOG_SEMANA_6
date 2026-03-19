# USER_STORIES.md — Historias de Usuario FleetGuard

## HU-01: Registrar vehículo con tipo asociado

**Como** administrador de flota     
**Quiero** registrar un nuevo vehículo indicando su placa, marca, modelo, tipo combustible y tipo de vehículo       
**Para** centralizar mi flota con la clasificación necesaria para aplicar reglas de mantenimiento.

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta HU es el punto de entrada del sistema. En si sin vehículos no hay nada. La asociación con tipo clave es porque las reglas se aplican por tipo
Como Actores tenemos al Administrador 
Dificultad **Pendiente revisar con Javier**
Impacto en el negocio: Alto
Reglas:
Placa Unica
VIN 17 caracteres
Estado Inicial ACTIVE
Tipo Obligatorio
Depende de: Ninguna es la primera HU
Habilita a HU-04, HU06. HU-11, HU-13

Gherkin
Feature: Registro de vehiculo con tipo asociado
    Scenario: Registro exitoso con datos validos
        Given un tipo de vehiculo "Camioneta" registrado en el sistema
        And no existe un vehiculo con placa "ABC-1234"
        When el administrador registra un vehiculo con placa "ABC-1234", marca "Toyota", modelo "Hilux", año 2023, combustible "Diesel", VIN "1HGBH41JXMN109186"  y tipo "Camioneta"
        Then el vehículo se crea con estado "ACTIVE" y kilometraje inicial del vehiculo


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

## HU-14 Asociar mantenimiento a regla aplicada

**Como** administrador de flota     
**Quiero** vincular el mantenimiento registrado a la regla que lo originó       
**Para** que el sistema reinicie el contador y sepa cuándo volver a generar una alerta para ese vehículo.

## HU-16 Registrar fecha y km del servicio

**Como** administrador de flota     
**Quiero** registrar la fecha y los kilometros que llevaba al momento del mantenimiento     
**Para** que el sistema calcule correctamente cuándo debe generarse la próxima alerta