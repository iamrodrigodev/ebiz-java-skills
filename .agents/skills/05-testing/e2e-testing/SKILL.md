---
name: e2e-testing
description: Guía oficial para pruebas End-to-End validando respuestas HTTP y JSON.
---

# Guía de Implementación: Pruebas E2E (End-to-End)

Las pruebas E2E verifican que el ensamblaje total de la Arquitectura Hexagonal funcione. Se validan las peticiones HTTP y los JSON resultantes.

## Escenarios Oficiales

### 1. Inserción (Prueba Positiva)
- **Método:** POST a `/api/usuarios`
- **Body:** JSON con datos válidos (`"origen": "I"`, `"tipoCuenta": "P"`, etc.)
- **Respuesta Esperada:** 
```json
// HTTP 201 Created
{ "usuarioId": 1 }
```

### 2. Duplicados con Manejo de Errores (Prueba Negativa)
Cuando la restricción de unicidad o la regla de negocio falla.
- **Método:** POST a `/api/usuarios` (Mismo Payload que el anterior)
- **Respuesta Esperada de nuestro GlobalExceptionHandler:**
```json
// HTTP 409 Conflict
{
    "codigo": "CORREO_ELECTRONICO_YA_REGISTRADO",
    "mensaje": "Ya existe un usuario registrado con el correo electrónico: miguel.calderon@empresa.com",
    "estadoHttp": 409,
    "fecha": "2026-07-30T16:39:16.795"
}
```
**NOTA DE BITÁCORA:** Al ejecutarse, Hibernate solo debe arrojar consultas `select count_big(*)` sobre la regla de negocio, evitando el error de `ConstraintViolationException` de SQL puro gracias a la validación preventiva de la Capa de Aplicación.

### 3. Consultas y Eliminaciones (404 Not Found)
Al pedir un identificador inexistente (ej. `GET /api/usuarios/9999`).
- **Respuesta Esperada:**
```json
// HTTP 404 Not Found
{
    "codigo": "USUARIO_NO_EXISTE",
    "mensaje": "No existe un usuario registrado con el identificador: 9999",
    "estadoHttp": 404,
    "fecha": "..."
}
```

## Instrucciones para el Agente
Asegúrate de comprobar estos flujos si te piden crear scripts automatizados de validación, utilizando `MockMvc` o simplemente proveyendo los JSON de prueba.
