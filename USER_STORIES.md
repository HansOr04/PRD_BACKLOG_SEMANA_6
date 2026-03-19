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