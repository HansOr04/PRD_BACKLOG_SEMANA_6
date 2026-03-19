# SUBTASKS.md — Tasking por Historia de Usuario

## HU-01: Registrar vehículo con tipo asociado

### Rol: DEV
1. Crear la tabla vehicle en la base de datos con campos: id, plate, brand, model, year, fuel_type, vin, status, current_mileage, vehicle_type_id
2. Crear la tabla vehicle_type en la base de datos con campos: id, name, description
3. Vincular vehicle con vehicle_type mediante llave foránea en vehicle_type_id
4. Definir los DTOs de entrada (placa, marca, modelo, año, combustible, VIN, tipo) y de salida (datos del vehículo creado)
5. Verificar que la placa sea única, que el VIN tenga exactamente 17 caracteres, que el tipo sea obligatorio y que el estado inicial sea ACTIVE
6. Exponer endpoint POST /api/vehicles
7. Retornar error claro si la placa ya existe, si el VIN es inválido o si el tipo de vehículo no fue encontrado

## HU-04: Registrar y acumular kilometraje

### Rol: DEV
1. Crear la tabla mileage_log en la base de datos con campos: id, vehicle_id, mileage_value, recorded_at, recorded_by
2. Vincular mileage_log con vehicle mediante llave foránea en vehicle_id
3. Definir DTOs de entrada (placa, valor de km, conductor) y de salida (registro creado y current_mileage actualizado)
4. Actualizar el current_mileage del vehículo cada vez que se registre un nuevo valor de km
5. Asegurarse de que el conductor quede registrado en recorded_by, es un campo obligatorio
6. Exponer endpoint POST /api/vehicles/{placa}/mileage
7. Retornar error claro si el vehículo no existe o si el conductor no fue especificado

## HU-05 Validar coherencia del kilometraje

### Rol: DEV
1. Verificar que el mileage_value ingresado sea mayor a cero
2. Si el vehículo no tiene kilometraje previo, aceptar cualquier valor mayor a cero como primer registro
3. Verificar que el mileage_value sea mayor al current_mileage actual del vehículo
4. Avisar en la respuesta si el incremento supera 2000 km respecto al último registro, sin bloquear el guardado
5. Retornar error claro si el km es negativo, igual o menor al actual

## HU-06: Consultar estado de mantenimiento del vehículo

### Rol: DEV
1. Consultar las reglas de mantenimiento que aplican según el tipo del vehículo
2. Comparar el kilometraje actual current_mileage, del vehículo contra el intervalo definido en cada regla
3. Determinar el estado del vehículo según la comparación:
   - AL_DIA: le faltan más km que el umbral para el próximo servicio
   - PROXIMO: está dentro del rango de advertencia
   - VENCIDO: ya superó el km límite definido en la regla
4. Manejar el caso donde el vehículo no tiene reglas configuradas para su tipo
5. Exponer endpoint GET /api/vehicles/{placa}/maintenance-status
6. Retornar error claro si el vehículo no existe

## HU-07 Crear regla con tipo de mantenimiento

### Rol: DEV
1. Crear la tabla maintenance_rule en la base de datos con campos: id, name, maintenance_type, interval_km, warning_threshold_km, status
2. Definir los DTOs de entrada (nombre, tipo de mantenimiento, intervalo en km, umbral de advertencia) y de salida (regla creada con su estado)
3. Verificar que el nombre sea obligatorio y que el interval_km sea mayor a cero
4. Asegurarse de que el warning_threshold_km sea configurable y opcional, con un valor por defecto si no se especifica
5. Registrar la regla con estado ACTIVE al momento de crearla
6. Exponer endpoint POST /api/maintenance-rules
7. Retornar error claro si el nombre está vacío o si el interval_km es cero o negativo

## HU-09 Asociar regla a tipo de vehículo

### Rol: DEV
1. Crear la tabla rule_vehicle_type_assoc en la base de datos con campos: id, rule_id, vehicle_type_id, created_at
2. Definir restricción única sobre la combinación rule_id + vehicle_type_id para evitar duplicados
3. Vincular rule_vehicle_type_assoc con maintenance_rule mediante llave foránea en rule_id
4. Vincular rule_vehicle_type_assoc con vehicle_type mediante llave foránea en vehicle_type_id
5. Definir los DTOs de entrada (id de regla, id de tipo de vehículo) y de salida (asociación creada)
6. Exponer endpoint POST /api/maintenance-rules/{id}/vehicle-types
7. Retornar error claro si la asociación ya existe o si la regla o el tipo de vehículo no fueron encontrados

## HU-11 Generar alerta automática por kilometraje

### Rol: DEV
1. Crear la tabla maintenance_alert en la base de datos con campos: id, vehicle_id, rule_id, status, triggered_at, due_at_km
2. Vincular maintenance_alert con vehicle mediante llave foránea en vehicle_id
3. Vincular maintenance_alert con maintenance_rule mediante llave foránea en rule_id
4. Comparar el current_mileage del vehículo contra el due_at_km de cada regla asociada a su tipo para saber si ya toca generar una alerta
5. Antes de crear una alerta, revisar que no exista ya una en estado PENDING para ese vehículo y esa regla
6. Configurar un proceso que corra automáticamente y revise todos los vehículos activos para generar alertas cuando corresponda
7. Retornar error claro si el vehículo o la regla no fueron encontrados