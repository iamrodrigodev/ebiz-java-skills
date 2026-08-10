---
name: manejo-global-de-errores-http
description: "Centralización y traducción de excepciones de negocio a status HTTP."
---

# Manejo Global de Errores HTTP

Cuando un Handler lanza una excepción de negocio (ej. `UsuarioNoExisteException`), esta no debe viajar suelta hasta el cliente web devolviendo un stacktrace de Java y un error 500 genérico.

**Implementación de Exception Handlers:**
1. **[OBLIGATORIO] Traducción Centralizada:** Se usa el patrón de Controller Advice (`@ControllerAdvice` o `@RestControllerAdvice`) en la capa REST para atrapar excepciones específicas y devolver un Payload estructurado (DTO) y un Status Code HTTP semántico (ej. 404 Not Found, 400 Bad Request, 409 Conflict).
2. **Separación CQRS Físico:**
   - **Ejemplo en el proyecto READ:** `ReadExceptionHandler` que retorna el DTO `ReadErrorResponse`.
   - **Ejemplo en el proyecto WRITE:** `WriteExceptionHandler` que retorna el DTO `WriteErrorResponse`.
   - Como están en runtimes separados, cada uno atrapa las excepciones de su propio Application module.
