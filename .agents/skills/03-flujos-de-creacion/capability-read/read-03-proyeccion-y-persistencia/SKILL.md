---
name: read-03-proyeccion-y-persistencia
description: "Pasos 6 al 10 de una capability READ: Projection, Repo, Adapter y Decorador readOnly."
---

# Creación de Capability READ (Pasos 6-10: Persistencia de Lectura)

La infraestructura física diseñada para leer rápdio.

**Paso 6: Projection (Opcional pero Recomendado)**
- Si la consulta devuelve pocos datos (ej. solo el nombre y correo), se crea un DTO o Interfaz de Proyección (Spring Data Projections) para evitar cargar el objeto JPA completo en memoria (optimización).
- **Ejemplos:** `UsuarioReadProjection`, `ConsultarUsuariosReadProjection`.

**Paso 7: Repository**
- Interfaz de Spring Data exclusiva de lectura. Devuelve Projections o Page. Suele usar JPQL o derived queries.
- **Ejemplos:** `UsuarioReadRepository`.

**Paso 8 y 9: ReadMapper y PersistenceAdapter**
- El Adapter implementa el Port-Out, ejecuta la query en el Repository, recibe la Projection, y usa el Mapper para convertirlo al `Result` requerido por Application.
- **Ejemplos:** `UsuarioReadMapper`, `UsuarioReadPersistenceAdapter`.

**Paso 10: TransactionalDecorator (Frontera Transaccional de Lectura)**
- Envuelve al Handler.
- **[OBLIGATORIO]** Debe usar explícitamente `@Transactional(readOnly=true)` para ganar rendimiento y evitar dirty checking de Hibernate.
- **Ejemplos:** `ConsultarUsuarioPorIdTransactionalDecorator`, `ConsultarUsuariosTransactionalDecorator`.
