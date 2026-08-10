---
name: respuestas-http-dtos
description: "DTOs para peticiones web y mapeadores hacia Application."
---

# Respuestas HTTP (DTOs y Mapeo)

El Contrato HTTP (la API REST) es un detalle de implementación. No se mezcla con el contrato de Application (Commands y Results).

**Mecánica de los DTOs de Infraestructura:**
1. **Los Requests:** Reciben el JSON del cliente. Pertenecen a la capa Web.
   - **Ejemplos el proyecto:** `CrearUsuarioRequest`, `ModificarUsuarioRequest`, `DarDeBajaUsuarioRequest`, `ConsultarUsuariosRequest`.
2. **Los Responses:** Devuelven el JSON al cliente. Permiten tener anotaciones como `@JsonProperty`.
   - **Ejemplos el proyecto:** `CrearUsuarioResponse`, `ConsultarUsuarioPorIdResponse`.

**El Puente (RestMapper):**
- **[OBLIGATORIO]** Debe existir un Mapper en Infraestructura que traduzca el `Request HTTP` en el `Command/Query` de Application, y el `Result` devuelto en un `Response HTTP`.
- **Ejemplos Mappers el proyecto:** `CrearUsuarioRestMapper`, `ConsultarUsuarioRestMapper`, `ModificarUsuarioRestMapper`.
- Esto permite que si cambia el formato de la API web, la capa de Application y Dominio ni se inmuten.
