---
name: read-04-rest-controller
description: "Pasos 11 y 12 de una capability READ: REST, Config, y Verificación."
---

# Creación de Capability READ (Pasos 11-12: REST y Fin)

Exponer la consulta a la red.

**Paso 11: REST (Request, Response, Mapper y Controller)**
- DTOs específicos para HTTP (Request puede no existir si son parámetros de ruta/query).
- **Controller GET:** Recibe HTTP GET, llama al Mapper, invoca al UseCase, devuelve Response DTO.
- **Ejemplos:** 
  - Request: `ConsultarUsuariosRequest`
  - Response: `ConsultarUsuarioPorIdResponse`
  - Mapper: `ConsultarUsuarioRestMapper`, `ConsultarUsuariosRestMapper`
  - Controller: `ConsultarUsuarioPorIdController`, `ConsultarUsuariosController`

**Paso 12: Configuración (Wiring) y Verify**
- Registrar explícitamente en `GestionUsuariosReadConfig` el Adapter, Handler (inyectándole el adapter) y Decorador (inyectándole el handler).
- Correr `mvn clean verify` en el runtime READ.
