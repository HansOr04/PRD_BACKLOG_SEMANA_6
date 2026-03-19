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

Feature: Registro de vehiculo con tipo asociado
    Scenario: Registro exitoso con datos validos
        Given un tipo de vehiculo "Camioneta" registrado en el sistema
        And no existe un vehiculo con placa "ABC-1234"
        When el administrador registra un vehiculo con placa "ABC-1234", marca "Toyota", modelo "Hilux", año 2023, combustible "Diesel", VIN "1HGBH41JXMN109186"  y tipo "Camioneta"
        Then el vehículo se crea con estado "ACTIVE" y kilometraje inicial del vehiculo
        
    Scenario: Rechazar placa duplicada
        Given ya existe un vehículo con placa "ABC-1234"
        When el administrador intenta registrar otro con placa "ABC-1234"
        Then el sistema rechaza indicando que la placa ya está en uso

    Scenario: Rechazar VIN inválido
        When el administrador registra un vehículo con VIN "12345"
        Then el sistema rechaza indicando que el VIN debe tener 17 caracteres

    Scenario: Rechazar sin tipo de vehículo
        When el administrador registra un vehículo sin tipo asociado
        Then el sistema rechaza indicando que el tipo es obligatorio


## HU-04 Registrar y acumular kilometraje

**Como** conductor     
**Quiero** registrar el kilometraje marcado en el odómetro de mi vehículo al terminar mi recorrido      
**Para** que el sistema mantenga el acumulado al día y pueda detectar cuando requiere mantenimiento.

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de usuario es la acción mas frecuente del sistema.Sin los km actualizados, las alertas no pueden calcularse.
Como actor tenemos al Conductor
Dificultad **Pendiente hablar con Javier**
Impacto Alto 
Regla: Se debe asociar a un conductor 
Depende de: HU-01
Habilita a HU-05, HU-06, HU-11

Feature: Registro y acumulación de kilometraje

  Scenario: Registro exitoso
    Given un vehículo "ACTIVE" con placa "ABC-1234" y km actual 45000
    When el conductor "María Torres" registra 45350 km
    Then se crea registro con valor 45350, fecha actual y conductor "María Torres"
    And el km acumulado del vehículo se actualiza a 45350

  Scenario: Registro asocia conductor responsable
    Given un vehículo activo
    When el conductor "María Torres" registra 46000 km
    Then el registro queda asociado a "María Torres" con fecha y hora actual


## HU-05 Validar coherencia del kilometraje

**Como** sistema     
**Quiero** validar la coherencia del kilometraje ingresado por el conductor      
**Para** evitar que datos incorrectos dañen el acumulado del vehículo y provoquen alertas equivocadas

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de Usuario es que se separa de la anterior HU debido a que tiene logica de validacion compleja
Ya que un Km incorrecto, podria arruinar por completo las alertas posteriores
Como Actor tenemos al Sistema
Dificultad **Pendiente hablar con Javier**
Impacto Alto
Regla Se utiliza la regla de negocio numero 1 que dice que no se puede tener un km menor al anterior registrado
Depende de HU-04
Habilita a : Integridad de HU-06 y HU-11

Feature: Validación de coherencia del kilometraje

  Scenario: Rechazar km menor al último
    Given un vehículo con km actual 45000
    When se registra 44500
    Then rechaza indicando que no puede ser menor a 45000

  Scenario: Rechazar km negativo
    When se registra -100
    Then rechaza indicando que debe ser positivo

  Scenario: Aceptar km igual al actual
    Given km actual 45000
    When se registra 45000
    Then acepta y el acumulado permanece en 45000

  Scenario: Advertir incremento excesivo
    Given km actual 45000
    When se registra 48000 (incremento > 2000 en un día)
    Then acepta con advertencia de incremento inusual

## HU-06 Consultar estado de mantenimiento del vehículo

**Como** administrador de flota o conductor     
**Quiero** consultar si existen mantenimientos pendientes del vehículo      
**Para** tomar acciones oportunas antes de que se venza el límite definido en la regla

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de usuario es que se necesita agregar una consulta activa del estado, ya que no podemos siempre esperar la alerta programada.
Esto le permite en si al administrador que consulte en cualquier momento si un vehiculo necesita servicio
Como actor tenemos al Sistema
Dificultad: **Pendiente hablar con Javier**
Impacto Alto
Depende de: HU-01 HU-04, HU-07 + HU-09
Habilita a: En si complementa a HU-11

Feature: Evaluación de estado de mantenimiento

  Scenario: Vehículo al día en mantenimiento
    Given un vehículo tipo "Camioneta" con km actual 5000
    And una regla "Cambio de aceite" cada 10000 km con umbral de 500 km asociada a "Camioneta"
    When el sistema evalúa el estado de mantenimiento del vehículo
    Then el estado es "AL_DIA"
    And indica que faltan 5000 km para el próximo servicio

  Scenario: Vehículo próximo a mantenimiento
    Given un vehículo tipo "Camioneta" con km actual 9600
    And una regla cada 10000 km con umbral de 500 km
    When el sistema evalúa el estado
    Then el estado es "PROXIMO"
    And indica que faltan 400 km para el servicio

  Scenario: Vehículo con mantenimiento vencido
    Given un vehículo tipo "Camioneta" con km actual 10500
    And una regla cada 10000 km
    When el sistema evalúa el estado
    Then el estado es "VENCIDO"
    And indica que se excedió por 500 km

  Scenario: Vehículo sin reglas asociadas a su tipo
    Given un vehículo tipo "Moto" sin reglas asociadas
    When el sistema evalúa el estado
    Then indica que no hay reglas de mantenimiento configuradas para ese tipo

## HU-07 Crear regla con tipo de mantenimiento

**Como** administrador de flota     
**Quiero** crear una regla de mantenimiento preventivo indicando el tipo de mantenimiento y el intervalo de kilometros en que debe realizarse      
**Para** tener definidas las condiciones bajo las cuales el sistema debe alertar que un vehículo requiere intervención 

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de usuario es que es el corazon del sistema ya que sin reglas no hay alertas
Como actor tenemos al administrador
Dificultad **Pendiente hablar con Javier**
Impacto Alto
Depende de: Nada
Habilita: HU-09, HU-06, HU-11, HU-14

Feature: Creación de regla de mantenimiento

  Scenario: Crear regla con intervalo km y umbral
    When el administrador crea "Cambio de aceite" con intervalo 10000 km y umbral 500 km
    Then la regla se crea con estado activo y queda disponible para asociar

  Scenario: Rechazar regla sin nombre
    When crea regla sin nombre
    Then rechaza indicando que es obligatorio

  Scenario: Rechazar intervalo en cero
    When crea regla con intervalo km 0
    Then rechaza indicando que debe ser mayor a cero

  Scenario: Crear regla con umbral personalizado
    When crea "Rotación de llantas" con 15000 km y umbral 1000 km
    Then se crea con umbral de advertencia de 1000 km

## HU-09 Asociar regla a tipo de vehículo

**Como** administrador de flota     
**Quiero** asociar un regla de mantenimiento a un tipo de vehículo      
**Para** que el sistema sepa qué condiciones de mantenimiento aplican a cada vehículo de la flota 

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de usaurio es para la reutilizacion de reglas, una vez configurada, podemos aplicarlas para todos los vehiculos del mismo tipo
Como actor tenemos al administrados
Dificultad: **Pendiente hablar con Javier**
Impacto Alto
Regla : Siempre las reglas se asocian a tipos de vehiculos
Depende de HU-07, HU-01
Habilita a HU-06,HU-11

Feature: Asociación regla a tipo de vehículo

  Scenario: Asociar regla a un tipo
    Given regla "Cambio de aceite" y tipo "Camioneta" existentes
    When el administrador los asocia
    Then todos los vehículos tipo "Camioneta" quedan sujetos a esa regla

  Scenario: Asociar a múltiples tipos
    Given regla "Revisión de frenos" y tipos "Camioneta" y "Sedán"
    When asocia a ambos
    Then la regla aplica para camionetas y sedanes

  Scenario: Rechazar asociación duplicada
    Given regla ya asociada a "Camioneta"
    When intenta asociar la misma regla al mismo tipo
    Then rechaza indicando que ya existe esa vinculación

## HU-11 Generar alerta automática por kilometraje

**Como** sistema     
**Quiero** generar una alerta automática cuando el kilometraje total de un vehículo alcance el límite definido de una regla      
**Para** notificar oportunamente que ese vehículo requiere mantenimiento

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de usuario es que es el nucleo del valor del producto ya que nos permite cambiar la gestion de reactiva a preventiva
Como Actor tneemos al Sistema
Dificultad: **Pendiente hablar con Javier**
Impacto Critico
Depende de HU-01, HU-04, HU-07+HU-09
Habilita a HU-12

Feature: Alerta automática por kilómetros

  Scenario: Generar alerta al alcanzar umbral
    Given vehículo con km 9500, regla cada 10000 km, umbral 500, último servicio a 0 km
    When el sistema evalúa las reglas
    Then genera alerta "PENDING" indicando servicio a 10000 km

  Scenario: No generar si está lejos del umbral
    Given vehículo con km 7000, regla cada 10000 km, umbral 500
    When evalúa
    Then no genera alerta

  Scenario: No duplicar alertas existentes
    Given vehículo con km 9600 y alerta "PENDING" existente para esa regla
    When evalúa
    Then no crea nueva alerta

  Scenario: No evaluar vehículos inactivos
    Given vehículo "INACTIVE" con km que supera umbral
    When evalúa
    Then no genera alerta

  Scenario: Alerta en umbral exacto
    Given vehículo con km 9500, regla cada 10000 km, umbral exacto 500
    When evalúa
    Then genera alerta "PENDING"

## HU-12 Consultar y clasificar alertas

**Como** administrador de flota o conductor     
**Quiero** consultar las alertas activas que tiene un vehículo y poderlas clasificar según su estado      
**Para** priorizar los mantenimientos más urgentes y mantener el vehículo al día y en operación

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de usuario es para cuando con multiples vehiculos el admin necesita saber cuales son urgentes vs cuales tienen margen.
Esta clasificacion permite toma de decisiones rapida
Como actor tenemos al administrados
Dificultad: **Pendiente hablar con Javier**
Impacto Alto
Regla: Las aleras son clasificadas por estado
Depende de HU-11
Habilita a HU-13

Feature: Consulta y clasificación de alertas

  Scenario: Filtrar alertas por estado PENDING
    Given existen 5 alertas PENDING, 3 WARNING y 2 OVERDUE
    When el administrador filtra por estado "PENDING"
    Then el sistema retorna las 5 alertas pendientes

  Scenario: Consultar todas las alertas activas
    Given existen alertas en diferentes estados
    When el administrador consulta sin filtro de estado
    Then retorna todas las alertas no resueltas con vehículo, regla, estado y fecha

  Scenario: Alerta escala de WARNING a OVERDUE
    Given una alerta en estado "WARNING" para un vehículo que superó el límite de km
    When el sistema re-evalúa las alertas
    Then el estado cambia a "OVERDUE"

  Scenario: No mostrar alertas resueltas por defecto
    Given existen 3 alertas "RESOLVED" y 2 "PENDING"
    When el administrador consulta alertas activas
    Then solo muestra las 2 "PENDING"

## HU-13 Registrar mantenimiento

**Como** administrador de flota     
**Quiero** registrar que un mantenimiento fue realizado sobre un vehículo       
**Para** dejar constancia del mantenimiento y cerrar la alerta generada

Como QA analizo la historia de Usuario para colocar los criterios de aceptación
El contexto de esta historia de usuario es que sin registrar el servicio, las alertas quedan abiertas para siempre
Como actor tenemos al administrador
Dificultad: **Pendiente hablar con Javier**
Impacto Alto
Depende de : HU-01 y HU-12
Habilita HU-14 y HU-16


## HU-14 Asociar mantenimiento a regla aplicada

**Como** administrador de flota     
**Quiero** vincular el mantenimiento registrado a la regla que lo originó       
**Para** que el sistema reinicie el contador y sepa cuándo volver a generar una alerta para ese vehículo.

## HU-16 Registrar fecha y km del servicio

**Como** administrador de flota     
**Quiero** registrar la fecha y los kilometros que llevaba al momento del mantenimiento     
**Para** que el sistema calcule correctamente cuándo debe generarse la próxima alerta