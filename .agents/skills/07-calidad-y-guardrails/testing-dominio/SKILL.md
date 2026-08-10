---
name: testing-dominio
description: "Cómo y qué probar en la Capa de Dominio."
---

# Testing del Dominio

Las reglas de negocio más complejas viven aquí. Al ser código puro Java, probarlo es extremadamente rápido.

**Reglas de Testing:**
- **[OBLIGATORIO] Herramientas:** Usar JUnit 5 y aserciones limpias (ej. AssertJ o Hamcrest).
- **[PROHIBIDO] Frameworks:** Absolutamente prohibido inicializar contextos de Spring Boot (`@SpringBootTest`, `@DataJpaTest`, `@ContextConfiguration`).
- **¿Qué evaluar?**
  - Que los Factory Methods de los agregados (`Usuario.crear`) instancien todo correctamente.
  - Que las reglas invariantes de los constructores lancen Excepciones si reciben datos inválidos.
  - Que los servicios / políticas puros de dominio (ej. `PoliticaVigenciaUsuarioEstandar`) calculen correctamente los estados según diferentes escenarios lógicos.
