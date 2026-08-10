---
name: read-02-orquestacion-handler
description: "Paso 5 de una capability READ: El Handler de Application."
---

# Creación de Capability READ (Paso 5: Handler)

El cerebro de la consulta.

**Paso 5: Handler**
- Orquesta la consulta utilizando estrictamente el Port-Out y devolviendo el Result.
- **Regla de Pureza Absoluta [PROHIBIDO]:** No usar anotaciones de Spring (ni `@Service`, ni `@Autowired`). Inyección únicamente por constructor.
- **[PROHIBIDO]** Retornar Entidades de Base de Datos directamente (ni de Dominio ni de JPA). Siempre retorna un DTO de Application (`Result` o `PageResult`).

**Ejemplos Reales en el proyecto:**
- `ConsultarUsuarioPorIdHandler`
- `ConsultarUsuariosHandler`

*Nota:* Si hay lógicas muy complejas de autorización o transformación, se testean en este nivel.
