---
name: write-04-rest-controller
description: "Pasos 9 al 11 de una capability WRITE: REST, Config, y Verificación local."
---

# Creación de Capability WRITE (Pasos 9-11: REST y Fin)

Última capa de Infraestructura: Exponer a la red.

**Paso 9: REST (DTOs, Mapper y Controller)**
- **Request/Response DTOs:** Define la entrada y salida HTTP, con validaciones de protocolo de Jakarta (ej. `@NotNull`).
- **RestMapper:** Traduce el `Request DTO` en el `Command` puro de Application.
- **Controller:** Recibe la petición HTTP, llama al Mapper, invoca al `UseCase` (que en tiempo de ejecución será el Decorador Transaccional) y retorna un status HTTP.
- **Ejemplos:** 
  - Request: `CrearUsuarioRequest`
  - Response: `CrearUsuarioResponse`
  - Mapper: `CrearUsuarioRestMapper`
  - Controller: `CrearUsuarioController`

**Paso 10: Configuración (Wiring explícito)**
- En la clase `GestionUsuariosWriteConfig`, crear los métodos `@Bean` para instanciar manualmente el Adapter, el Handler inyectándole el Adapter, y el Decorator inyectándole el Handler.

**Paso 11: Verificación**
- Ejecutar `mvn clean verify` + levantar el runtime `[proyecto]-write` y hacer un smoke test.
