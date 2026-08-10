---
name: write-03-persistencia
description: "Pasos 6 al 8 de una capability WRITE: Entity, Adapter, y Decorador Transaccional."
---

# Creación de Capability WRITE (Pasos 6-8: Persistencia)

Entramos en la capa de Infraestructura (detalle tecnológico).

**Paso 6: JPA Entity y Repository**
- Entidades diseñadas como detalle de infraestructura de base de datos.
- **[OBLIGATORIO] Segregación:** Estas entidades son exclusivas para WRITE.
- **Ejemplos:** `UsuarioJpaEntity`, `PersonaJpaEntity`, `UsuarioWriteRepository`.

**Paso 7: Mapper y PersistenceAdapter (Implementar el Port-Out)**
- El Adapter es la clase real que implementa la interfaz Port-Out definida en el Paso 3.
- Utiliza el Mapper para convertir entidades de Dominio a JPA Entities y viceversa.
- Llama al Repository de Spring Data.
- **Ejemplos:** `UsuarioWriteMapper`, `UsuarioWritePersistenceAdapter`, `ConsultarPersonaAdapter`.

**Paso 8: Frontera Transaccional (TransactionalDecorator)**
- Patrón de diseño fundamental para no contaminar Application.
- Implementa el `UseCase` (Port-In).
- Recibe el `Handler` real por inyección.
- Expone el método rodeado con `@Transactional` (de Spring) y dentro invoca al `Handler`.
- **Ejemplos:** `CrearUsuarioTransactionalDecorator`, `ModificarUsuarioTransactionalDecorator`, `DarDeBajaUsuarioTransactionalDecorator`.
- **Validación:** ¿La transacción vive en un decorator de infraestructura? SÍ.
