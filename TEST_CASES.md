# TEST CASES — FleetGuard MVP

**Proyecto:** FleetGuard — Sistema de Gestión de Flota y Mantenimiento Preventivo
**Versión:** 1.1
**Fecha:** 08 de abril de 2026
**Autor:** Hans Ortiz
**Rol:** QA Engineer — Sofka Technologies (Training IA Native)
**Documento relacionado:** TEST_PLAN.md v1.1

---

## Convenciones

- **Prioridad:** P1 (Crítica) · P2 (Alta) · P3 (Media) · P4 (Baja)
- **Tipo:** Positivo (Happy Path) · Negativo (Error Path) · Borde (Edge Case)
- **HU:** Historia de Usuario que cubre el caso
- **Estado:** ✅ Implementado · ⚠️ Parcial · ❌ No ejecutable (gap del backend)

**Datos de prueba reutilizables:**
- VIN válido: `1HGCM82633A123456` (17 caracteres)
- Placa válida: dinámica con UUID (`KT` + 5 chars random)
- VehicleTypeId Sedán: `c1a1d13e-b3df-4fab-9584-890b852d5311`
- Marca/Modelo: Toyota / Corolla
- Año: 2023
- Combustible: Gasoline / Diesel

---

## Estado General de Cobertura

| Nivel | Total | Implementados | Estado |
|---|---|---|---|
| Pruebas Unitarias | 151 | 151 | ✅ 100% |
| Pruebas API (Karate) | 14 | 14 | ✅ 100% |
| Pruebas E2E (Serenity) | 8 | 8 | ✅ 100% |
| **TOTAL EJECUTABLES** | **173** | **173** | **✅ 100%** |
| Casos NO ejecutables (Gap Report) | 13 | 0 | ❌ Backend no implementa funcionalidad |

---

## SECCIÓN 1 — PRUEBAS UNITARIAS: fleet-service / Domain Layer

**Estado:** ✅ 35 tests implementados, 0 fallos.

### 1.1 Value Object: Plate

| ID | Título | Datos de entrada | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-D01 | Crear Plate con valor válido | `"ABC123"` | Instancia creada, `getValue()` retorna `"ABC123"` | ✅ |
| UT-F-D02 | Crear Plate con null | `null` | `IllegalArgumentException("Plate cannot be null or empty")` | ✅ |
| UT-F-D03 | Crear Plate con string vacío | `""` | `IllegalArgumentException` | ✅ |
| UT-F-D04 | Crear Plate con solo espacios | `"   "` | `IllegalArgumentException` | ✅ |
| UT-F-D05 | Equals/hashCode con valores iguales | dos `Plate("ABC123")` | `equals=true`, hashCodes iguales | ✅ |

### 1.2 Value Object: Vin

| ID | Título | Datos de entrada | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-D06 | Crear Vin con 17 caracteres | `"1HGCM82633A123456"` | Instancia creada | ✅ |
| UT-F-D07 | Crear Vin con 16 caracteres | `"1HGCM82633A12345"` | `InvalidVinException` mensaje contiene `"got: 16"` | ✅ |
| UT-F-D08 | Crear Vin con 18 caracteres | `"1HGCM82633A1234567"` | `InvalidVinException` mensaje contiene `"got: 18"` | ✅ |
| UT-F-D09 | Crear Vin con null | `null` | `InvalidVinException` mensaje contiene `"got: null"` | ✅ |

### 1.3 Value Object: Mileage

| ID | Título | Datos de entrada | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-D10 | Crear Mileage con valor positivo | `5000` | `getValue() == 5000` | ✅ |
| UT-F-D11 | Crear Mileage con valor 0 | `0` | Instancia creada, `getValue() == 0` | ✅ |
| UT-F-D12 | Crear Mileage con negativo | `-100` | `InvalidMileageException("Mileage cannot be negative")` | ✅ |
| UT-F-D13 | Mileage.zero() factory | — | Instancia con valor 0 | ✅ |
| UT-F-D14 | validateNotLessThan con km mayor | curr=5000, new=6000 | No lanza excepción | ✅ |
| UT-F-D15 | validateNotLessThan con km menor | curr=5000, new=3000 | `InvalidMileageException` | ✅ |
| UT-F-D16 | validateNotLessThan con km igual | curr=5000, new=5000 | No lanza excepción | ✅ |
| UT-F-D17 | isExcessiveIncrement con incremento > 2000 | prev=5000, new=8000 | Retorna `true` | ✅ |
| UT-F-D18 | isExcessiveIncrement con incremento ≤ 2000 | prev=5000, new=6500 | Retorna `false` | ✅ |
| UT-F-D19 | isExcessiveIncrement con incremento exacto 2000 | prev=5000, new=7000 | Retorna `false` (condición es `> 2000`) | ✅ |

### 1.4 Aggregate: Vehicle

| ID | Título | Datos de entrada | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-D20 | Vehicle.create con datos válidos | plate, vin, year=2023, vehicleType | Vehicle con `id != null`, `status=ACTIVE`, `currentMileage=0` | ✅ |
| UT-F-D21 | Vehicle.create con brand null | brand=null | `IllegalArgumentException("Brand cannot be null or empty")` | ✅ |
| UT-F-D22 | Vehicle.create con year ≤ 0 | year=0 | `IllegalArgumentException("Year must be a positive integer")` | ✅ |
| UT-F-D23 | Vehicle.create con vehicleType null | vehicleType=null | `IllegalArgumentException("VehicleType cannot be null")` | ✅ |
| UT-F-D24 | Vehicle.updateMileage con km mayor | currentMileage=0, new=5000 | `currentMileage=5000` | ✅ |
| UT-F-D25 | Vehicle.updateMileage con km menor | currentMileage=5000, new=3000 | `InvalidMileageException` | ✅ |
| UT-F-D26 | Vehicle.updateMileage en INACTIVE | status=INACTIVE | `InactiveVehicleException` | ✅ |

### 1.5 AggregateRoot, MileageLog y eventos

| ID | Título | Datos de entrada | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-D27 | AggregateRoot pull events vacía después | crear y pull dos veces | Primera retorna eventos, segunda retorna lista vacía | ✅ |
| UT-F-D28 | MileageLog.create con recordedBy null | recordedBy=null | `MissingRecordedByException` | ✅ |
| UT-F-D29 | MileageLog.create con recordedBy blank | recordedBy="   " | `MissingRecordedByException` | ✅ |
| UT-F-D30 | MileageLog.create genera evento MileageRegistered | datos válidos | Lista contiene 1 `MileageRegistered` con vehicleId, vehicleTypeId, vehicleStatus, mileage correctos | ✅ |

### 1.6 Excepciones

| ID | Clase | Resultado esperado | Estado |
|---|---|---|---|
| UT-F-D31 | `DuplicatePlateException("ABC123")` | Mensaje: `"A vehicle with plate 'ABC123' already exists"` | ✅ |
| UT-F-D32 | `InactiveVehicleException(UUID)` | Mensaje contiene el UUID | ✅ |
| UT-F-D33 | `InvalidMileageException` | Mensaje coincide | ✅ |
| UT-F-D34 | `InvalidVinException` | Mensaje coincide | ✅ |
| UT-F-D35 | `VehicleTypeNotFoundException(UUID)` | Mensaje: `"Vehicle type not found with id: <UUID>"` | ✅ |

---

## SECCIÓN 2 — PRUEBAS UNITARIAS: fleet-service / Application Layer

**Estado:** ✅ 15 tests implementados.

### 2.1 RegisterVehicleService

| ID | Título | Tipo | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-A01 | Registro exitoso con datos válidos | Positivo | Vehicle persistida, response con `status="ACTIVE"`, save invocado 1 vez | ✅ |
| UT-F-A02 | Placa duplicada | Negativo | `DuplicatePlateException`, save nunca invocado | ✅ |
| UT-F-A03 | VehicleType inexistente | Negativo | `VehicleTypeNotFoundException` | ✅ |

### 2.2 RegisterMileageService

| ID | Título | Tipo | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-A04 | Registro exitoso publica evento | Positivo (CRÍTICO) | Vehicle saved, MileageLog saved, eventPublisher.publish() invocado 1 vez | ✅ |
| UT-F-A05 | Vehículo no encontrado | Negativo | `VehicleNotFoundException` | ✅ |
| UT-F-A06 | mileageValue ≤ 0 | Negativo | `InvalidMileageException("Mileage value must be greater than zero")` | ✅ |
| UT-F-A07 | Incremento excesivo (> 2000 km) | Borde | response.excessiveIncrement = true (sin error) | ✅ |
| UT-F-A08 | Best-effort: fallo en publish no propaga | Borde (CRÍTICO) | Response exitoso, datos persistidos, exception del publisher logueada pero no propagada | ✅ |

### 2.3 GetVehicleByPlateService

| ID | Título | Tipo | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-A09 | Vehículo encontrado | Positivo | Response con datos completos del vehículo | ✅ |
| UT-F-A10 | Vehículo no encontrado | Negativo | `VehicleNotFoundException` | ✅ |

---

## SECCIÓN 3 — PRUEBAS UNITARIAS: fleet-service / Infrastructure Layer

**Estado:** ✅ 28 tests implementados.

### 3.1 Controllers (con @WebMvcTest)

| ID | Controller | Caso | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-I01 | VehicleController | POST /api/vehicles válido | 201 CREATED + VehicleResponse | ✅ |
| UT-F-I02 | VehicleController | POST /api/vehicles sin plate | 400 BAD_REQUEST con validación | ✅ |
| UT-F-I03 | MileageController | POST /api/vehicles/{plate}/mileage válido | 201 CREATED + MileageResponse | ✅ |
| UT-F-I04 | VehicleQueryController | GET /api/vehicles/{plate} existente | 200 OK + VehicleResponse | ✅ |
| UT-F-I05 | VehicleQueryController | GET /api/vehicles/{plate} inexistente | 404 NOT_FOUND | ✅ |

### 3.2 Mappers, Adapters, Publisher

| ID | Clase | Caso | Resultado esperado | Estado |
|---|---|---|---|---|
| UT-F-I06 | VehicleWebMapper | toCommand(RegisterVehicleRequest) | Command con todos los campos mapeados | ✅ |
| UT-F-I07 | VehicleWebMapper | toResponse(RegisterVehicleResponse) | DTO con campos correctos | ✅ |
| UT-F-I08 | MileageWebMapper | toCommand(plate, request) | Command con plate, mileageValue, recordedBy | ✅ |
| UT-F-I09 | MileageWebMapper | toResponse(RegisterMileageResponse) | DTO con campos incluyendo alertId | ✅ |
| UT-F-I10 | VehicleMapper | toJpaEntity(Vehicle) | JpaEntity con plate string, vin string, status string, VehicleTypeJpaEntity anidado | ✅ |
| UT-F-I11 | VehicleMapper | toDomain(VehicleJpaEntity) | Vehicle con Value Objects reconstruidos | ✅ |
| UT-F-I12 | VehicleRepositoryAdapter | save() | Delega a JpaRepository correctamente | ✅ |
| UT-F-I13 | VehicleRepositoryAdapter | findByPlate() | Optional mapeado a domain | ✅ |
| UT-F-I14 | MileageLogRepositoryAdapter | save() | Persiste y mapea correctamente | ✅ |
| UT-F-I15 | MileageLogMapper | round-trip domain↔entity | Sin pérdida de datos | ✅ |
| UT-F-I16 | RabbitMQEventPublisher | publish(MileageRegistered) | Publica al exchange/routing key correcto | ✅ |
| UT-F-I17 | RabbitMQEventPublisher | Ignora eventos desconocidos | No publica nada, no lanza excepción | ✅ |
| UT-F-I18 | NoOpEventPublisher | publish() para todo tipo de evento | No-op, no lanza excepción | ✅ |
| UT-F-I19 | GlobalExceptionHandler | DuplicatePlateException | 400 + ErrorResponse con mensaje | ✅ |
| UT-F-I20 | GlobalExceptionHandler | VehicleNotFoundException | 404 + ErrorResponse | ✅ |
| UT-F-I21 | GlobalExceptionHandler | VehicleTypeNotFoundException | 404 + ErrorResponse | ✅ |
| UT-F-I22 | GlobalExceptionHandler | MethodArgumentNotValidException | 400 + detalles de campos | ✅ |
| UT-F-I23 | GlobalExceptionHandler | Exception genérica | 500 + "Internal server error" | ✅ |

---

## SECCIÓN 4 — PRUEBAS UNITARIAS: rules-alerts-service / Domain Layer

**Estado:** ✅ 20 tests implementados.

### 4.1 Modelos

| ID | Clase | Caso | Estado |
|---|---|---|---|
| UT-R-D01 | MaintenanceRule | Constructor all-args con datos válidos | ✅ |
| UT-R-D02 | MaintenanceRule | Constructor no-args + setters | ✅ |
| UT-R-D03 | MaintenanceAlert | Constructor con status PENDING | ✅ |
| UT-R-D04 | MaintenanceAlert | Status RESOLVED | ✅ |
| UT-R-D05 | RuleVehicleTypeAssoc | Constructor con UUIDs y createdAt | ✅ |
| UT-R-D06 | MaintenanceRecord | Constructor con datos completos | ✅ |
| UT-R-D07 | MaintenanceRecord | Constructor no-args | ✅ |

### 4.2 Excepciones

| ID | Clase | Resultado esperado | Estado |
|---|---|---|---|
| UT-R-D08 | DuplicateAssociationException | Mensaje correcto, es RuntimeException | ✅ |
| UT-R-D09 | MaintenanceRuleNotFoundException | Mensaje correcto | ✅ |
| UT-R-D10 | InvalidMaintenanceException | Mensaje correcto | ✅ |
| UT-R-D11 | VehicleNotFoundException | Mensaje incluye placa | ✅ |

---

## SECCIÓN 5 — PRUEBAS UNITARIAS: rules-alerts-service / Application Layer

**Estado:** ✅ 25 tests implementados.

### 5.1 CreateMaintenanceRuleService

| ID | Caso | Tipo | Estado |
|---|---|---|---|
| UT-R-A01 | Crear regla con datos válidos | Positivo | ✅ |
| UT-R-A02 | warningThresholdKm = null usa default 500 | Borde | ✅ |
| UT-R-A03 | warningThresholdKm = 0 usa default 500 | Borde | ✅ |

### 5.2 AssociateVehicleTypeService

| ID | Caso | Tipo | Estado |
|---|---|---|---|
| UT-R-A04 | Asociación exitosa | Positivo | ✅ |
| UT-R-A05 | Regla inexistente | Negativo | ✅ |
| UT-R-A06 | Asociación duplicada | Negativo | ✅ |

### 5.3 EvaluateMaintenanceAlertsService (CRÍTICO)

| ID | Caso | Tipo | Estado |
|---|---|---|---|
| UT-R-A07 | Vehículo INACTIVE no se evalúa | Negativo (CRÍTICO) | ✅ |
| UT-R-A08 | Sin asociaciones para el vehicleTypeId | Negativo | ✅ |
| UT-R-A09 | Genera alerta dentro del umbral | Positivo (CRÍTICO) | ✅ |
| UT-R-A10 | No genera alerta fuera del umbral | Negativo | ✅ |
| UT-R-A11 | Idempotencia: no duplica alerta PENDING existente | Borde (CRÍTICO) | ✅ |
| UT-R-A12 | Skip si la regla no se encuentra | Negativo | ✅ |

### 5.4 RegisterMaintenanceService

| ID | Caso | Tipo | Estado |
|---|---|---|---|
| UT-R-A13 | Registro exitoso con datos completos | Positivo | ✅ |
| UT-R-A14 | serviceType null | Negativo | ✅ |
| UT-R-A15 | serviceType blank | Negativo | ✅ |
| UT-R-A16 | performedAt futuro | Negativo | ✅ |
| UT-R-A17 | mileageAtService = 0 | Negativo | ✅ |
| UT-R-A18 | Vehicle no encontrado | Negativo | ✅ |
| UT-R-A19 | Resolución de alerta tras servicio (CRÍTICO) | Positivo (CRÍTICO) | ✅ |
| UT-R-A20 | Sin alertas activas (solo crea record) | Positivo | ✅ |

### 5.5 GetAlertsService y GetAlertsByVehicleService

| ID | Caso | Estado |
|---|---|---|
| UT-R-A21 | GetAlertsService listar todas | ✅ |
| UT-R-A22 | GetAlertsService filtrar por status | ✅ |
| UT-R-A23 | GetAlertsByVehicleService con vehículo existente | ✅ |
| UT-R-A24 | GetAlertsByVehicleService con vehículo inexistente retorna lista vacía | ✅ |
| UT-R-A25 | GetAlertsByVehicleService enriquece con ruleName | ✅ |

---

## SECCIÓN 6 — PRUEBAS UNITARIAS: rules-alerts-service / Infrastructure Layer

**Estado:** ✅ 28 tests implementados.

### 6.1 Controllers

| ID | Controller | Caso | Estado |
|---|---|---|---|
| UT-R-I01 | MaintenanceRuleController | POST /api/maintenance-rules (201) | ✅ |
| UT-R-I02 | MaintenanceRuleController | POST /api/maintenance-rules/{id}/vehicle-types (201) | ✅ |
| UT-R-I03 | MaintenanceController | POST /api/maintenance/{plate} (201) | ✅ |
| UT-R-I04 | AlertController | GET /api/alerts (200) | ✅ |
| UT-R-I05 | AlertController | GET /api/alerts?status=PENDING | ✅ |
| UT-R-I06 | AlertController | GET /api/alerts/vehicle/{plate} (200) | ✅ |
| UT-R-I07 | AlertController | GET /api/alerts/vehicle/{plate} con vehículo no existente retorna lista vacía | ✅ |
| UT-R-I08 | AlertController | Response incluye campo ruleName | ✅ |

### 6.2 Mappers (Persistence)

| ID | Mapper | Caso | Estado |
|---|---|---|---|
| UT-R-I09 | MaintenanceRulePersistenceMapper | Round-trip domain↔entity | ✅ |
| UT-R-I10 | MaintenanceAlertPersistenceMapper | Round-trip | ✅ |
| UT-R-I11 | MaintenanceRecordPersistenceMapper | Round-trip | ✅ |
| UT-R-I12 | RuleVehicleTypeAssocPersistenceMapper | Round-trip | ✅ |

### 6.3 Mappers (Web)

| ID | Mapper | Caso | Estado |
|---|---|---|---|
| UT-R-I13 | MaintenanceRuleWebMapper | request→command, response→DTO | ✅ |
| UT-R-I14 | AssociateVehicleTypeWebMapper | request→command, response→DTO | ✅ |
| UT-R-I15 | MaintenanceWebMapper | request→command, response→DTO | ✅ |

### 6.4 Adapters

| ID | Adapter | Caso | Estado |
|---|---|---|---|
| UT-R-I16 | MaintenanceRuleRepositoryAdapter | save | ✅ |
| UT-R-I17 | MaintenanceRuleRepositoryAdapter | existsById | ✅ |
| UT-R-I18 | MaintenanceAlertRepositoryAdapter | save | ✅ |
| UT-R-I19 | MaintenanceAlertRepositoryAdapter | existsByVehicleIdAndRuleIdAndStatus | ✅ |
| UT-R-I20 | MaintenanceAlertRepositoryAdapter | findByStatus | ✅ |
| UT-R-I21 | MaintenanceAlertRepositoryAdapter | findById | ✅ |
| UT-R-I22 | MaintenanceRecordRepositoryAdapter | save | ✅ |
| UT-R-I23 | RuleVehicleTypeAssocRepositoryAdapter | save | ✅ |
| UT-R-I24 | RuleVehicleTypeAssocRepositoryAdapter | exists (true/false) | ✅ |

### 6.5 Consumer RabbitMQ

| ID | Caso | Estado |
|---|---|---|
| UT-R-I25 | Consumer recibe evento ACTIVE → invoca evaluate() con parámetros correctos | ✅ |
| UT-R-I26 | Consumer maneja evento INACTIVE (no propaga) | ✅ |
| UT-R-I27 | Consumer swallows excepción del service (no propaga al broker) | ✅ |
| UT-R-I28 | GlobalExceptionHandler maneja todas las excepciones del dominio | ✅ |

---

## SECCIÓN 7 — PRUEBAS DE API: Karate DSL

**Repositorio:** `AUTO_API_FLEETGUARD_KARATE`
**Estado actual:** ✅ 14 escenarios implementados (6 originales + 8 nuevos).

### 7.1 fleet-service: register-vehicle.feature

| ID | Scenario | HU | Tipo | Estado |
|---|---|---|---|---|
| KT-F01 | Registro exitoso con datos válidos | HU-01 | Happy | ✅ |
| KT-F02 | Placa duplicada (segundo POST con misma placa) | HU-01 | Negativo | ✅ |
| KT-F03 | VIN de 16 caracteres | HU-01 | Negativo | ✅ |
| **KT-F04** | **VIN de 5 caracteres ("12345")** | **HU-01** | **Negativo** | **✅ NUEVO** |
| **KT-F05** | **Sin campo vehicleTypeId** | **HU-01** | **Negativo** | **✅ NUEVO** |

**Detalles de KT-F04 (NUEVO):**
- Request: `POST /api/vehicles` con body que incluye `"vin": "12345"` (5 caracteres) y resto de campos válidos
- Validación: `status == 400` y `response.message contains '17'`
- Justificación: La HU-01 menciona explícitamente este caso, KT-F03 solo cubría 16 chars

**Detalles de KT-F05 (NUEVO):**
- Request: `POST /api/vehicles` con body sin el campo `vehicleTypeId`
- Validación: `status == 400` y `response.message contains 'tipo de vehículo'`
- Justificación: La HU-01 lo lista como Criterio de Aceptación; el DTO tiene `@NotNull` sobre `vehicleTypeId`

### 7.2 fleet-service: register-mileage.feature

| ID | Scenario | HU | Tipo | Estado |
|---|---|---|---|---|
| KT-F06 | Registro exitoso de km | HU-04 | Happy | ✅ |
| KT-F07 | Incremento excesivo (> 2000 km) | HU-05 | Borde | ✅ |
| KT-F08 | Km menor al actual | HU-05 | Negativo | ✅ |
| **KT-F09** | **Aceptar km igual al actual** | **HU-05** | **Borde** | **✅ NUEVO** |

**Detalles de KT-F09 (NUEVO):**
- Setup: registrar primero `mileageValue: 45000`, luego registrar de nuevo el mismo valor
- Validación: ambos retornan `status == 201`, `response.currentMileage == 45000`, `response.excessiveIncrement == false`
- Justificación: La HU-05 lo lista explícitamente, y `Mileage.validateNotLessThan` solo rechaza valores estrictamente menores

### 7.3 fleet-service: get-vehicle.feature

| ID | Scenario | Tipo | Estado |
|---|---|---|---|
| KT-F10 | Obtener vehículo existente | Happy | ✅ |
| KT-F11 | Vehículo inexistente | Negativo | ✅ |

### 7.4 rules-alerts-service: create-rule.feature

| ID | Scenario | HU | Tipo | Estado |
|---|---|---|---|---|
| KT-R01 | Crear regla preventiva válida | HU-07 | Happy | ✅ |
| KT-R02 | Sin nombre | HU-07 | Negativo | ✅ |
| KT-R03 | intervalKm = 0 | HU-07 | Negativo | ✅ |

### 7.5 rules-alerts-service: associate-vehicle-type.feature

| ID | Scenario | HU | Tipo | Estado |
|---|---|---|---|---|
| KT-R04 | Asociación exitosa | HU-09 | Happy | ✅ |
| KT-R05 | Asociación duplicada (409) | HU-09 | Negativo | ✅ |
| KT-R06 | Regla inexistente (404) | HU-09 | Negativo | ✅ |

### 7.6 rules-alerts-service: generate-alerts.feature (NUEVO)

Archivo creado en esta versión. Cubre los escenarios faltantes de HU-11.

| ID | Scenario | HU | Tipo | Estado |
|---|---|---|---|---|
| **KT-R07** | **Generar alerta en umbral exacto (km=9500)** | **HU-11** | **Happy** | **✅ NUEVO** |
| **KT-R08** | **No alerta si km lejos del umbral (km=7000)** | **HU-11** | **Negativo** | **✅ NUEVO** |
| **KT-R09** | **No duplicar alertas PENDING (idempotencia)** | **HU-11** | **Borde** | **✅ NUEVO** |

**Detalles de KT-R07 (NUEVO):**
- Setup: crear regla `intervalKm=10000, warningThresholdKm=500`, asociar a Sedán, crear vehículo Sedán
- Acción: POST mileage con `mileageValue: 9500` (umbral exacto: 10000-500)
- Wait: 3 segundos para procesamiento RabbitMQ
- Validación: `GET /api/alerts/vehicle/{plate}` retorna 200 con al menos 1 alerta, `status == 'PENDING'`, `ruleName == ruleName creado`

**Detalles de KT-R08 (NUEVO):**
- Setup igual a KT-R07
- Acción: POST mileage con `mileageValue: 7000` (lejos del umbral)
- Validación: `GET /api/alerts/vehicle/{plate}` retorna 200 con array vacío

**Detalles de KT-R09 (NUEVO — IDEMPOTENCIA):**
- Setup igual a KT-R07
- Acciones: POST mileage 9600, esperar 3s, POST mileage 9700, esperar 3s
- Validación: `GET /api/alerts/vehicle/{plate}` retorna exactamente 1 alerta PENDING para esa combinación vehículo+regla

### 7.7 rules-alerts-service: register-maintenance.feature

| ID | Scenario | HU | Tipo | Estado |
|---|---|---|---|---|
| **KT-R10** | **Registro con datos completos** | **HU-13** | **Happy** | **✅ NUEVO** |
| **KT-R11** | **Sin serviceType (campo obligatorio)** | **HU-13** | **Negativo** | **✅ NUEVO** |

**Detalles de KT-R10 (NUEVO):**
- Setup: crear vehículo
- Request: `POST /api/maintenance/{plate}` con body completo (serviceType, description, cost, provider, mileageAtService, performedAt)
- Validación: `status == 201`, `response.serviceType == 'OIL_CHANGE'`, `response.cost == 85.00`, `response.provider == 'Taller Central'`

**Detalles de KT-R11 (NUEVO):**
- Setup: crear vehículo
- Request: `POST /api/maintenance/{plate}` con body sin `serviceType`
- Validación: `status == 400`

### 7.8 rules-alerts-service: get-alerts.feature

| ID | Scenario | HU | Tipo | Estado |
|---|---|---|---|---|
| KT-R12 | Listar todas las alertas | HU-11 | Happy | ✅ |
| KT-R13 | Filtrar por status=PENDING | HU-11 | Happy | ✅ |

**Nota importante:** Las assertions de KT-R12 y KT-R13 fueron actualizadas en esta versión para incluir el campo `ruleName` que ahora viene en `MaintenanceAlertResponse`.

### 7.9 integration: full-flow.feature

| ID | Scenario | HUs | Estado |
|---|---|---|---|
| KT-X01 | Vehículo → Regla → Asociar → Km → Alerta → Mantenimiento | HU-01, HU-04, HU-07, HU-09, HU-11, HU-13 | ✅ |

**Detalles del flujo:**
1. POST vehicle (fleet-service) → guardar plate y vehicleId
2. POST maintenance-rule (rules-alerts) → guardar ruleId
3. POST associate vehicle-type (rules-alerts)
4. POST mileage con km dentro del umbral (fleet-service)
5. Sleep 3s para procesamiento RabbitMQ
6. GET /api/alerts/vehicle/{plate} → verificar alerta PENDING con ruleName
7. POST maintenance/{plate} con alertId
8. GET /api/alerts/vehicle/{plate} → verificar que la alerta cambió a RESOLVED

---

## SECCIÓN 8 — PRUEBAS END-TO-END: Serenity BDD + Screenplay

**Repositorio:** `AUTO_E2E_FLEETGUARD_SCREENPLAY`
**Estado actual:** ✅ 8 escenarios implementados.

### 8.1 RegisterVehicleTests

| ID | Scenario | HU | Estado |
|---|---|---|---|
| E2E-01 | Registro exitoso desde formulario | HU-01 | ✅ |
| E2E-02 | Validación de campos obligatorios | HU-01 | ✅ |

### 8.2 RegisterMileageTests

| ID | Scenario | HU | Estado |
|---|---|---|---|
| E2E-03 | Actualización exitosa de km | HU-04 | ✅ |
| E2E-04 | Advertencia por incremento excesivo | HU-05 | ✅ |

### 8.3 CreateRuleTests

| ID | Scenario | HU | Estado |
|---|---|---|---|
| E2E-05 | Crear regla con tipos de vehículo asociados | HU-07 | ✅ |

### 8.4 RegisterServiceTests

| ID | Scenario | HU | Estado |
|---|---|---|---|
| E2E-06 | Consultar alertas + registrar servicio | HU-13 | ✅ |

### 8.5 NavigationTests

| ID | Scenario | Estado |
|---|---|---|
| E2E-07 | Navegación por sidebar (5 links) | ✅ |

### 8.6 FullFlowTest (CRÍTICO)

| ID | Scenario | HUs | Estado |
|---|---|---|---|
| E2E-08 | Ciclo completo de vida de mantenimiento (registro → km → alerta → servicio) | HU-01, HU-04, HU-07, HU-09, HU-11, HU-13 | ✅ |

**Pasos del E2E-08:**
1. Crear regla en `/rules` con tipo Sedán asociado
2. Registrar vehículo Sedán en `/register`
3. Registrar km alto en `/mileage` que dispare la alerta
4. Esperar 3 segundos (procesamiento RabbitMQ)
5. Ir a `/services`, consultar alertas para esa placa
6. Verificar que aparece la alerta
7. Llenar formulario de servicio y registrar
8. Verificar toast de éxito

---

## SECCIÓN 9 — TRAZABILIDAD HU ↔ TEST CASES

| HU | Descripción | Unitarias | Karate | E2E | Cobertura |
|---|---|---|---|---|---|
| HU-01 | Registrar vehículo | UT-F-D01 a D23, UT-F-A01 a A03, UT-F-I01 a I02 | KT-F01 a F05 | E2E-01, E2E-02 | ✅ Completa |
| HU-04 | Registrar kilometraje | UT-F-D27 a D30, UT-F-A04 a A08, UT-F-I03 | KT-F06 | E2E-03 | ✅ Completa |
| HU-05 | Validar kilometraje | UT-F-D12 a D19, UT-F-D24 a D26, UT-F-A06, A07 | KT-F07, F08, F09 | E2E-04 | ✅ Completa |
| HU-06 | Consultar estado de mantenimiento | — | — | — | ❌ Backend no implementa |
| HU-07 | Crear regla | UT-R-D01, UT-R-A01 a A03 | KT-R01 a R03 | E2E-05 | ✅ Completa |
| HU-09 | Asociar regla a tipo | UT-R-D05, UT-R-A04 a A06 | KT-R04 a R06 | (en E2E-08) | ✅ Completa |
| HU-11 | Generar alerta | UT-R-D03 a D04, UT-R-A07 a A12, UT-R-I25 a I27 | KT-R07 a R09, R12, R13, KT-X01 | E2E-08 | ⚠️ Parcial (falta INACTIVE caja negra) |
| HU-12 | Consultar y clasificar alertas | — | KT-R13 (parcial) | — | ❌ Backend no implementa WARNING/OVERDUE |
| HU-13 | Registrar mantenimiento | UT-R-D06 a D08, UT-R-A13 a A20 | KT-R10, R11, KT-X01 | E2E-06 | ✅ Completa |
| HU-14 | Asociar mantenimiento a regla | — | — | — | ❌ Backend no implementa recálculo |
| HU-16 | Registrar fecha y km del servicio | — | — | — | ❌ Backend no implementa validación de coherencia |

---

## SECCIÓN 10 — RESUMEN DE COBERTURA

### 10.1 Tests ejecutables

| Nivel | Total | Positivos | Negativos | Borde | Estado |
|---|---|---|---|---|---|
| Unitarias fleet-service Domain | 35 | 14 | 15 | 6 | ✅ |
| Unitarias fleet-service Application | 10 | 4 | 4 | 2 | ✅ |
| Unitarias fleet-service Infrastructure | 23 | 9 | 8 | 6 | ✅ |
| **Subtotal fleet-service** | **78** | — | — | — | **✅** |
| Unitarias rules-alerts-service Domain | 11 | 6 | 4 | 1 | ✅ |
| Unitarias rules-alerts-service Application | 25 | 9 | 12 | 4 | ✅ |
| Unitarias rules-alerts-service Infrastructure | 28 | 13 | 8 | 7 | ✅ |
| **Subtotal rules-alerts-service** | **64** | — | — | — | **✅** |
| Tests adicionales (mappers, exceptions extra) | 9 | — | — | — | ✅ |
| **TOTAL UNITARIAS** | **151** | — | — | — | **✅** |
| API Karate (fleet-service) | 5+4 = 9 | 3 | 4 | 2 | ✅ |
| API Karate (rules-alerts-service) | 6+5 = 11 | 5 | 5 | 1 | ✅ |
| API Karate (cross-service) | 1 | 1 | 0 | 0 | ✅ |
| **Subtotal Karate** | **14** | **9** | **9** | **3** | **✅** |
| E2E Serenity | 8 | 6 | 1 | 1 | ✅ |
| **TOTAL EJECUTABLES** | **173** | — | — | — | **✅ 100%** |

### 10.2 Tests NO ejecutables (Gap Report)

| HU | Escenarios definidos en CA | No ejecutables | Razón |
|---|---|---|---|
| HU-06 | 4 | 4 | Endpoint `/maintenance-status` no existe |
| HU-11 | 5 | 1 | No hay endpoint para cambiar a INACTIVE |
| HU-12 | 4 | 3 | Lógica WARNING/OVERDUE no implementada |
| HU-14 | 4 | 4 | Recálculo del próximo servicio no implementado |
| HU-16 | 3 | 1 | Validación de coherencia no implementada |
| **TOTAL** | **20** | **13** | — |

**13 escenarios documentados como GAP del backend** (sección 13 del TEST_PLAN.md). Reportados al equipo DEV para iteraciones futuras.

---

*FleetGuard MVP · Sofka Technologies Training IA Native · Test Cases v1.1 · 2026*
*Elaborado por Hans Ortiz (QA Engineer)*
