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

### 2.3 Sistema Automatico
Descripcion: Sistema backend que valida datos y genera alertas sin intervención humana.
HU relacionadas: HU-05, HU-11.
