# SUBTASKS.md — Tasking por Historia de Usuario

## HU-01: Registrar vehículo con tipo asociado

### Rol: DEV
1. Crear la tabla vehicle en la base de datos con campos: id, plate, brand, model, year, fuel_type, vin, status, current_mileage, vehicle_type_id
2. Crear la tabla vehicle_type en la base de datos con campos: id, name, description
3. Vincular vehicle con vehicle_type mediante llave foránea en vehicle_type_id
4. Configurar DTOs de entrada (placa, marca, modelo, año, combustible, VIN, tipo) y de salida (datos del vehículo creado)
5. Implementar validaciones: placa única, VIN de 17 caracteres, tipo obligatorio, estado inicial ACTIVE
6. Exponer endpoint POST /api/vehicles
7. Implementar manejo de errores: placa duplicada, VIN inválido, tipo no encontrado

### HU-04: Registrar y acumular kilometraje

### Rol: DEV
1. Crear tabla mileage_log en la base de datos con campos: id, vehicle_id, mileage_value, recorded_at, recorded_by
2. Vincular mileage_log con vehicle mediante llave foránea en vehicle_id
3. Configurar DTOs de entrada (placa, valor de km, conductor) y de salida (registro creado y km acumulado actualizado)
4. Implementar lógica de acumulación: actualizar current_mileage en vehicle con el nuevo valor registrado
5. Implementar asociación obligatoria del conductor en recorded_by
6. Exponer endpoint POST /api/vehicles/{placa}/mileage
7. Implementar manejo de errores: vehículo no encontrado, conductor no especificado

## HU-05 Validar coherencia del kilometraje

### Rol: DEV
1. Implementar validación: mileage_value debe ser mayor a cero
2. Implementar manejo del caso inicial: si el vehículo no tiene kilometraje previo, aceptar cualquier valor mayor a cero
3. Implementar validación: mileage_value debe ser mayor al current_mileage actual del vehículo
4. Implementar advertencia en respuesta si el incremento supera 2000 km respecto al último registro (no bloquea el guardado)
5. Implementar manejo de errores: km negativo, km igual o menor al actual