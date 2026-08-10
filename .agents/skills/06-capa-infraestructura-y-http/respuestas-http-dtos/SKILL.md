---
name: respuestas-http-dtos
description: DTOs de red y mapeo HTTP.
---

# Respuestas HTTP (DTOs)
- Los `Response` DTOs (ej. `ConsultarUsuarioPorIdResponse`) son exclusivamente para el Controller (infraestructura).
- Se usa un `RestMapper` para traducir de `Result` (Application) a `Response` (HTTP).
