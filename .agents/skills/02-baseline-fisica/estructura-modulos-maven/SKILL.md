---
name: estructura-modulos-maven
description: "Fronteras y responsabilidades de cada módulo Maven."
---

# Baseline Arquitectónica (Estructura de Módulos Maven)

La solución está organizada como un reactor Maven multimódulo. Cada módulo tiene responsabilidades súper específicas:

1. **`[proyecto]-domain`**: Contiene el dominio puro y compartido por todos. Aquí viven los Agregados, Value Objects, Enums y Políticas (Ej: `Usuario`, `PersonaId`).
2. **`[proyecto]-application-read`**: Contiene exclusivamente consultas (queries), Port-In, Port-Out, Handlers y los Results de las operaciones de lectura (Ej: `ConsultarUsuarioPorIdQuery`).
3. **`[proyecto]-application-write`**: Contiene exclusivamente modificaciones de estado (commands), Port-In, Port-Out, Handlers y Excepciones de negocio (Ej: `CrearUsuarioCommand`).
4. **`[proyecto]-read`**: La capa de Infraestructura para lectura. Contiene adapters, JPA Entity READ, decoradores transaccionales, REST Controllers y la clase de arranque (Bootstrap) en el puerto :8081 (Ej: `UsuarioReadJpaEntity`).
5. **`[proyecto]-write`**: La capa de Infraestructura para escritura. Contiene adapters, JPA Entity WRITE, decoradores transaccionales, REST Controllers y la clase de arranque (Bootstrap) en el puerto :8082 (Ej: `UsuarioJpaEntity`).
6. **`[proyecto]-architecture-tests`**: Contiene las reglas de ArchUnit y guardrails transversales para automatizar la validación de dependencias.
7. **`database`**: Scripts de base de datos aislados del runtime de la aplicación.
