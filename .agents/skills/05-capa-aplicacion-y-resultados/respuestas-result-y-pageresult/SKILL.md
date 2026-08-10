---
name: respuestas-result-y-pageresult
description: "Contratos de salida de Application (DTOs de respuesta internos)."
---

# Respuestas Puras de Application (Result y PageResult)

Cuando un caso de uso (Handler) termina su orquestación y debe devolver datos, nunca devuelve entidades de dominio en bruto ni modelos de base de datos. Devuelve un contrato explícito de "Resultado".

**Reglas de Diseño:**
1. **[OBLIGATORIO] Desacoplamiento:** Los objetos `Result` (para un elemento) y `PageResult` (para listados) son clases/records que viven en la capa de Application.
2. **[PROHIBIDO] Anotaciones Contaminantes:** Al ser parte de Application, los Results NO DEBEN TENER anotaciones de Jackson (ej. `@JsonProperty`, `@JsonIgnore`) ni de JPA o Spring. Son simples Data Transfer Objects agnósticos.

**Ejemplos Reales en el proyecto (Capa Application-Read):**
- `ConsultarUsuarioPorIdResult`
- `ConsultarUsuariosResult`
- `ConsultarUsuariosPageResult`

**Ejemplos Reales en el proyecto (Capa Application-Write):**
- `CrearUsuarioResult` (Utilizado si el Command necesita devolver el ID del usuario recién creado).
