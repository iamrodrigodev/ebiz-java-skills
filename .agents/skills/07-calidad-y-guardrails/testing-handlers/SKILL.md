---
name: testing-handlers
description: "Mocking e inyección para probar los Handlers orquestadores."
---

# Testing de Handlers (Application Pura)

El Handler orquesta llamados a la base de datos (vía puertos) y llamadas al dominio. Para testear esta lógica SIN tocar una base de datos real, usamos "Mocks" puros.

**Reglas de Testing:**
- **[OBLIGATORIO] Mocks sin Spring:** Se deben mockear las dependencias externas (los Port-Out). Ej: Usar Mockito puro (`@Mock`, `@InjectMocks` o instanciar a mano pasando mocks por el constructor).
- **[PROHIBIDO] Spring Boot Test:** No usar `@SpringBootTest` ni `@MockBean` (anotaciones de Spring). El test debe ejecutar en milisegundos.
- **¿Qué evaluar?**
  - **Happy Path:** Si el puerto devuelve los datos correctos, que el dominio se modifique correctamente y que el Handler retorne el `Result` esperado.
  - **Error Path:** Comprobar que el Handler lanza explícitamente las excepciones de negocio (ej. `UsuarioNoExisteException`) si el Port-Out retorna vacío. Usar `assertThrows`.
