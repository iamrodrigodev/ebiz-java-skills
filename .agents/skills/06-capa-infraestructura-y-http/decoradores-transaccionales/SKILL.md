---
name: decoradores-transaccionales
description: "Uso del patrón Decorator para extraer las anotaciones transaccionales de Application."
---

# Frontera Transaccional (Transactional Decorators)

La lógica transaccional de Base de Datos es pura infraestructura. Application no debe saber qué es un `@Transactional`.

**¿Cómo funciona el Decorator?**
1. El Decorator es una clase que vive en la capa de **Infraestructura**.
2. Implementa la interfaz `UseCase` (la misma interfaz que implementa el Handler).
3. Recibe la instancia del Handler real inyectado por constructor.
4. El método implementado anota explícitamente `@Transactional` y delega la ejecución al Handler real.

**Reglas Estrictas:**
- **WRITE:** `@Transactional` normal.
- **READ:** Debe usar `@Transactional(readOnly = true)`.
- **[PROHIBIDO]** Duplicar lógica de negocio dentro del Decorador. Su única responsabilidad es envolver la llamada en una transacción.

**Ejemplos de Referencia en el proyecto:**
- WRITE: `CrearUsuarioTransactionalDecorator`, `ModificarUsuarioTransactionalDecorator`, `DarDeBajaUsuarioTransactionalDecorator`, `EliminarFisicamenteUsuarioTransactionalDecorator`, `EliminarLogicamenteUsuarioTransactionalDecorator`.
- READ: `ConsultarUsuarioPorIdTransactionalDecorator`, `ConsultarUsuariosTransactionalDecorator`.
