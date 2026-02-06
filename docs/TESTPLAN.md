# Plan de Pruebas - Sistema de Gestión de Cuentas de Estudiantes

## Descripción General
Este plan de pruebas cubre la lógica empresarial actual de la aplicación COBOL para el Sistema de Gestión de Cuentas de Estudiantes. El plan incluye casos de prueba para funcionalidades de lectura de saldo, crédito, débito y validación de fondos.

## Matriz de Casos de Prueba

| ID | Descripción del Caso de Prueba | Condiciones Previas | Pasos de la Prueba | Resultado Esperado | Resultado Real | Estado | Comentarios |
|----|--|--|--|--|--|--|--|
| TC-001 | Ver saldo inicial | Sistema iniciado, saldo inicial = 1000.00 | 1. Seleccionar opción "1. View Balance" 2. Observar saldo mostrado | Se muestra saldo actual de 1000.00 | | | Caso base: validar saldo inicial correcto |
| TC-002 | Realizar crédito válido | Sistema iniciado, saldo = 1000.00 | 1. Seleccionar "2. Credit Account" 2. Ingresar monto: 500.00 3. Observar nuevo saldo | Saldo aumenta a 1500.00. Mensaje: "Amount credited. New balance: 1500.00" | | | Prueba operación crediticia exitosa |
| TC-003 | Realizar débito válido (fondos suficientes) | Sistema iniciado, saldo = 1000.00 | 1. Seleccionar "3. Debit Account" 2. Ingresar monto: 300.00 3. Observar nuevo saldo | Saldo disminuye a 700.00. Mensaje: "Amount debited. New balance: 700.00" | | | Prueba operación de débito exitosa |
| TC-004 | Rechazar débito (fondos insuficientes) | Sistema iniciado, saldo = 1000.00 | 1. Seleccionar "3. Debit Account" 2. Ingresar monto: 1500.00 3. Observar mensaje de error | Operación rechazada. Mensaje: "Insufficient funds for this debit." Saldo permanece en 1000.00 | | | Validación crítica: prevenir saldos negativos |
| TC-005 | Débito exacto (saldo completo) | Sistema iniciado, saldo = 500.00 | 1. Seleccionar "3. Debit Account" 2. Ingresar monto: 500.00 3. Observar nuevo saldo | Saldo se vuelve 0.00. Mensaje: "Amount debited. New balance: 0.00" | | | Caso límite: agotar saldo completamente |
| TC-006 | Crédito después de débito | Sistema iniciado, saldo = 1000.00 | 1. Débito de 200.00 2. Verificar nuevo saldo (800.00) 3. Crédito de 300.00 4. Verificar saldo final | Saldo final: 1100.00 | | | Prueba secuencia de operaciones |
| TC-007 | Múltiples créditos consecutivos | Sistema iniciado, saldo = 1000.00 | 1. Crédito 1: +100.00 2. Crédito 2: +200.00 3. Crédito 3: +150.00 4. Ver saldo final | Saldo final: 1450.00 | | | Validar acumulación correcta de créditos |
| TC-008 | Múltiples débitos consecutivos | Sistema iniciado, saldo = 1000.00 | 1. Débito 1: -100.00 2. Débito 2: -200.00 3. Débito 3: -150.00 4. Ver saldo final | Saldo final: 550.00 | | | Validar acumulación correcta de débitos |
| TC-009 | Opción de menú inválida | Sistema iniciado | 1. Seleccionar "5" (opción no válida) 2. Observar respuesta del sistema | Mensaje: "Invalid choice, please select 1-4." 3. Menú se redisplaya | | | Validar manejo de entrada inválida |
| TC-010 | Opción de menú inválida (letra) | Sistema iniciado | 1. Seleccionar "A" (entrada no numérica) 2. Observar respuesta del sistema | Sistema maneja error gracefully o muestra mensaje de error apropiado | | | Prueba robustez de entrada |
| TC-011 | Salir del programa | Sistema iniciado | 1. Seleccionar opción "4. Exit" | Mensaje: "Exiting the program. Goodbye!" Programa termina | | | Validar salida limpia |
| TC-012 | Crédito con monto cero | Sistema iniciado, saldo = 1000.00 | 1. Seleccionar "2. Credit Account" 2. Ingresar monto: 0.00 3. Ver saldo | Saldo permanece 1000.00 o se muestra mensaje apropiado | | | Caso límite: monto nulo |
| TC-013 | Débito con monto cero | Sistema iniciado, saldo = 1000.00 | 1. Seleccionar "3. Debit Account" 2. Ingresar monto: 0.00 3. Ver saldo | Saldo permanece 1000.00; operación se procesa sin cambios | | | Caso límite: monto nulo |
| TC-014 | Crédito con monto muy grande | Sistema iniciado, saldo = 1000.00 | 1. Seleccionar "2. Credit Account" 2. Ingresar monto: 999999.99 3. Ver nuevo saldo | Saldo se actualiza. Validar que no hay overflow (máximo: 9(6)V99 = 999999.99) | | | Prueba límite superior de campo numérico |
| TC-015 | Precisión decimal - crédito | Sistema iniciado, saldo = 1000.00 | 1. Seleccionar "2. Credit Account" 2. Ingresar monto: 123.45 3. Ver nuevo saldo | Nuevo saldo: 1123.45 (precisión de 2 decimales) | | | Validar manejo correcto de centavos |
| TC-016 | Precisión decimal - débito | Sistema iniciado, saldo = 1000.50 | 1. Seleccionar "3. Debit Account" 2. Ingresar monto: 100.25 3. Ver nuevo saldo | Nuevo saldo: 900.25 (precisión de 2 decimales conservada) | | | Validar operaciones con decimales |
| TC-017 | Débito parcial desde saldo bajo | Sistema iniciado, saldo = 200.00 | 1. Seleccionar "3. Debit Account" 2. Ingresar monto: 150.00 3. Ver nuevo saldo | Saldo se reduce a 50.00 | | | Validar débito parcial válido |
| TC-018 | Débito rechazado desde saldo bajo | Sistema iniciado, saldo = 200.00 | 1. Seleccionar "3. Debit Account" 2. Ingresar monto: 250.00 | Mensaje: "Insufficient funds for this debit." Saldo permanece 200.00 | | | Validar rechazo cuando se intenta exceder |
| TC-019 | Secuencia: Ver → Crédito → Ver → Débito → Ver | Sistema iniciado, saldo = 1000.00 | 1. View Balance (esperar 1000.00) 2. Credit +500 (esperar 1500.00) 3. View Balance (esperar 1500.00) 4. Debit -400 (esperar 1100.00) 5. View Balance (esperar 1100.00) | Cada paso muestra el saldo correcto esperado | | | Prueba completa de flujo del usuario |
| TC-020 | Persistencia de saldo entre operaciones | Sistema iniciado | 1. Crédito de 250.00 (verificar saldo = 1250.00) 2. Débito de 150.00 (verificar saldo = 1100.00) 3. Salir 4. Reiniciar programa | Nota: En COBOL actual, saldo se reinicia a 1000.00 (sin persistencia en BD). Este comportamiento debe documentarse | Saldo reinicia a 1000.00 | | | Identificar que no hay persistencia a archivo |

## Resumen de Cobertura

### Funcionalidades Cubiertas
- ✅ Lectura de saldo (View Balance)
- ✅ Operación de crédito (Credit Account)
- ✅ Operación de débito (Debit Account)
- ✅ Validación de fondos suficientes
- ✅ Manejo de menú y opciones
- ✅ Entrada y salida del programa

### Casos Límite Cubiertos
- ✅ Saldo cero
- ✅ Monto cero
- ✅ Monto muy grande (overflow)
- ✅ Fondos insuficientes
- ✅ Precisión decimal
- ✅ Entrada inválida

### Reglas de Negocio Validadas
- ✅ Saldo no negativo (validación en débito)
- ✅ Créditos aumentan saldo correctamente
- ✅ Débitos disminuyen saldo correctamente
- ✅ Operaciones secuenciales mantienen consistencia
- ✅ Rechazo de operaciones inválidas

## Notas para Migración a Node.js

1. **Persistencia**: El plan de pruebas actual asume saldo en memoria (se reinicia). Para Node.js, implementar persistencia en base de datos (MongoDB, PostgreSQL, etc.)

2. **Validaciones adicionales**: En la migración a Node.js, considere añadir:
   - Validación de formato de entrada (regex para números)
   - Límites de crédito/débito configurables
   - Estados de cuenta (ACTIVA, MORA, INACTIVA)
   - Auditoría con timestamp de operaciones
   - ID de estudiante como identificador único

3. **Framework sugerido**: Express.js con endpoints REST:
   - `GET /account/:id/balance` (View Balance)
   - `POST /account/:id/credit` (Credit Account)
   - `POST /account/:id/debit` (Debit Account)

4. **Pruebas en Node.js**: Usar Jest o Mocha para automatizar estos casos de prueba con aserciones.

5. **Casos de prueba adicionales para Node.js**:
   - Manejo de concurrencia (operaciones simultáneas)
   - Validación de autenticación/autorización
   - Transacciones atómicas
   - Manejo de errores de base de datos
   - Rate limiting
   - Validación de entrada en backend (no solo en frontend)
