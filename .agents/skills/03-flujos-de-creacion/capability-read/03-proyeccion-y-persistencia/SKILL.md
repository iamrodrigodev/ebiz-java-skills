---
name: read-proyeccion-persistencia
description: Pasos 6 al 10 de READ (Projection, Repo, Adapter, Decorator).
---

# Creación Capability READ (Pasos 6-10: Persistencia)
6. **Projection:** Crear si la consulta no necesita toda la entidad (para rendimiento).
7. **Repository:** Devuelve projection/page (JPQL/derived query).
8. **ReadMapper:** Projection -> Result.
9. **PersistenceAdapter:** Implementa Port-Out.
10. **TransactionalDecorator:** Aplicar `@Transactional(readOnly=true)`.
