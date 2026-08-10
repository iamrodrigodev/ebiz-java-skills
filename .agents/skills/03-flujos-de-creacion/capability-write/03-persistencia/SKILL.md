---
name: write-persistencia
description: Pasos 6, 7 y 8 de WRITE (Entity, Adapter, Decorator).
---

# Creación Capability WRITE (Pasos 6-8: Persistencia)
6. **JPA Entity / Repository:** Diseño de base de datos (`UsuarioJpaEntity`).
7. **Mapper y PersistenceAdapter:** El adapter implementa los Port-Out definidos por Application.
8. **TransactionalDecorator:** Introduce la transacción (`@Transactional`) envolviendo al Handler sin contaminar Application.
