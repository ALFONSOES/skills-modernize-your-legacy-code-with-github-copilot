# Plan de Pruebas - Sistema de Gestión de Cuentas de Estudiantes

## Descripción General
Este plan valida la lógica empresarial actual implementada en COBOL (lectura de saldo, crédito, débito, validaciones básicas). Incluye instrucciones para que cada caso sea independiente y reproducible en la aplicación tal como está.

## Limitaciones de la aplicación COBOL actual
- Saldo en memoria sólo durante la ejecución: el programa inicializa `STORAGE-BALANCE` a 1000.00 en cada arranque; no hay persistencia.
- Solo existe una cuenta/global balance (no hay múltiples estudiantes ni IDs).
- No hay auditoría ni autenticación.
- Entradas se aceptan vía consola; el formato y validación son mínimos.

> Nota: Para ejecutar casos con un saldo distinto al inicial, los pasos de prueba incluyen acciones previas (crédito/débito) para establecer el saldo requerido.

## Matriz de Casos de Prueba (casos independientes)

| ID | Descripción | Condiciones Previas (acción para preparar) | Pasos de la Prueba | Resultado Esperado | Resultado Real | Estado | Comentarios |
|----|-------------|---------------------------------------------|--------------------|--------------------|----------------|--------|-------------|
| TC-001 | Ver saldo inicial | Aplicación iniciada (saldo por defecto = 1000.00) | 1. Seleccionar `1` (View Balance) | Mostrar `Current balance: 1000.00` | | | Validar valor por defecto |
| TC-002 | Crédito válido | Iniciar app. Para asegurar saldo base: (ninguno) | 1. Seleccionar `2` 2. Ingresar `500.00` | Mensaje: `Amount credited. New balance: 1500.00` | | | Si difiere, registrar pasos previos usados |
| TC-003 | Débito válido (fondos suficientes) | Iniciar app. (si saldo ≠ 1000, preparar con crédito/débito) | 1. Seleccionar `3` 2. Ingresar `300.00` | Mensaje: `Amount debited. New balance: 700.00` | | | Validar resta correcta |
| TC-004 | Rechazo débito (fondos insuficientes) | Iniciar app. (saldo = 1000) | 1. Seleccionar `3` 2. Ingresar `1500.00` | Mensaje: `Insufficient funds for this debit.` Saldo no cambia | | | Verificado condición de rechazo |
| TC-005 | Débito exacto (agotar saldo) | Iniciar app. Para tener 500: 1) Seleccionar `3` y debitar 500 o 2) reiniciar y preparar saldo a 500 mediante pasos previos | 1. Seleccionar `3` 2. Ingresar `500.00` | Saldo = `0.00` y mensaje de éxito | | | Si no se puede preparar, documentar razón |
| TC-006 | Secuencia: Débito → Crédito (independiente) | Iniciar app. Preparar saldo: (ninguno) | 1. (Setup) Seleccionar `3` y debitar `200` 2. Seleccionar `2` y acreditar `300` 3. Ver saldo | Saldo final esperado: `1100.00` | | | Cada operación valida intermedia debe confirmarse |
| TC-007 | Múltiples créditos consecutivos | Iniciar app. (ninguno) | 1. `2` +100 2. `2` +200 3. `2` +150 4. Ver saldo | Saldo final `1450.00` | | | Ver acumulación correcta |
| TC-008 | Múltiples débitos consecutivos | Iniciar app. (ninguno) | 1. `3` 100 2. `3` 200 3. `3` 150 4. Ver saldo | Saldo final `550.00` | | | Ver acumulación correcta |
| TC-009 | Menú: opción inválida (número) | Iniciar app. | 1. Introducir `5` | Mensaje: `Invalid choice, please select 1-4.` Menú se vuelve a mostrar | | | Manejo de entrada inválida |
| TC-010 | Menú: entrada no numérica | Iniciar app. | 1. Introducir `A` (o carácter) | Comportamiento razonable: mostrar mensaje de error o ignorar entrada sin crash | | | Registrar comportamiento actual para decidir estándar |
| TC-011 | Salir del programa | Iniciar app. | 1. Seleccionar `4` | Mensaje: `Exiting the program. Goodbye!` Programa finaliza | | | Validar salida ordenada |
| TC-012 | Crédito con monto cero | Iniciar app. | 1. Seleccionar `2` 2. Ingresar `0.00` | Saldo no cambia. Mensaje apropiado o sin efecto | | | Decidir política: permitir o rechazar monto 0 |
| TC-013 | Débito con monto cero | Iniciar app. | 1. Seleccionar `3` 2. Ingresar `0.00` | Saldo no cambia. Mensaje apropiado o sin efecto | | | Decidir política: permitir o rechazar monto 0 |
| TC-014 | Crédito con monto máximo permitido | Iniciar app. | 1. Seleccionar `2` 2. Ingresar `999999.99` | Saldo actualizado sin overflow (límite 9(6)V99) | | | Verificar límite del campo numérico COBOL |
| TC-015 | Precisión decimal - crédito | Iniciar app. | 1. Seleccionar `2` 2. Ingresar `123.45` | Saldo refleja `+123.45` con 2 decimales | | | Ver manejo de centavos |
| TC-016 | Precisión decimal - débito | Iniciar app. Preparar saldo a `1000.50` (setup: acreditar 0.50) | 1. Seleccionar `3` 2. Ingresar `100.25` | Saldo `900.25` | | | Ver decimales mantenidos |
| TC-017 | Débito parcial desde saldo bajo | Iniciar app. Preparar saldo a `200.00` (setup previo) | 1. Seleccionar `3` 2. Ingresar `150.00` | Saldo `50.00` | | | Validar comportamiento con saldos bajos |
| TC-018 | Débito rechazado desde saldo bajo | Iniciar app. Preparar saldo a `200.00` (setup) | 1. Seleccionar `3` 2. Ingresar `250.00` | Mensaje: rechazo por fondos insuficientes; saldo sin cambios | | | Validación clave de seguridad financiera |
| TC-019 | Flujo completo independiente | Iniciar app. | 1. Ver saldo 2. `2` +500 3. Ver saldo 4. `3` -400 5. Ver saldo | Cada paso muestra saldo esperado en secuencia; final `1100.00` | | | Probar flujo típico de usuario en una sola sesión |
| TC-020 | Persistencia entre ejecuciones (documentación) | Iniciar app. | 1. `2` +250 2. `3` -150 3. Salir 4. Reiniciar app 5. Ver saldo | En la implementación COBOL actual: saldo vuelve a `1000.00`. Documentar este comportamiento y marcar como requisito para migración si se requiere persistencia | `1000.00` | | Importante para migración a Node.js |

## Resumen y cambios aplicados
- He actualizado las "Condiciones Previas" para que cada caso sea reproducible sin depender de otros tests; cuando se requiere otro saldo, el paso de preparación (setup) aparece en esa columna.
- He documentado las limitaciones clave de la aplicación COBOL actual que afectan la validez de pruebas (persistencia, una sola cuenta, sin auditoría).

## Recomendaciones para ejecutar las pruebas actuales
- Ejecutar cada caso en una sesión nueva salvo aquellos que explícitamente prueben flujos en una misma sesión.
- Para preparar saldos distintos al defecto, use pasos de setup (créditos/débitos) antes de ejecutar la verificación principal.
- Registrar siempre el "Resultado Real" y añadir captura de pantalla o registro de consola cuando sea posible.

## Notas para migración a Node.js
- Mantener estos casos como base, pero tras migrar a Node.js convertirlos a pruebas automatizadas (unitarias/integración) con persistencia real y objetos `account` identificados por `id`.

Mando a revisar el archivo y puedo además:
- Generar un conjunto de scripts de preparación que automatizan los "setup steps" para cada caso.
- Dividir la tabla en múltiples archivos por categoría (básico, límites, integración) si prefiere mayor legibilidad.