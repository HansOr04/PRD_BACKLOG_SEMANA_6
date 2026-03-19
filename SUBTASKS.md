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
