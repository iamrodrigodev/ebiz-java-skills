---
name: testing-handlers
description: Cómo probar los Handlers de Application.
---

# Testing de Handlers (Application)
- Los tests de Handler se escriben con mocks de Port-Out (ej. usando Mockito puro).
- **Regla estricta:** No requieren ni deben cargar el contexto de Spring (`@SpringBootTest`).
