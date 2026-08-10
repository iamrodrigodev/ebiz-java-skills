---
name: reglas-validacion-jakarta
description: Validación de peticiones de entrada.
---

# Validaciones Jakarta (Protocolo)
- Los `Request` DTOs deben contener validaciones de protocolo (`@NotNull`, `@NotBlank`) para evitar que basura llegue al Command/Query.
