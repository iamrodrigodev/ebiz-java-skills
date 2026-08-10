---
name: persistencia-aislada
description: "Aislamiento a nivel entidad JPA entre Read y Write."
---

# Persistencia Aislada (CQRS a nivel Base de Datos)

En una arquitectura convencional, un `@Entity` gigante de JPA se usa para leer y para guardar. En CQRS físico esto está **PROHIBIDO**.

**Reglas de Diseño:**
1. **[PROHIBIDO] Compartir Entidades JPA:** Las entidades mapeadas a Hibernate para insertar registros NO son las mismas que se usan para listar registros.
2. **Runtime WRITE:**
   - Usa entidades optimizadas para inserción/actualización y bloqueo (locks).
   - **Ejemplos el proyecto:** `UsuarioJpaEntity`, `PersonaJpaEntity`, `UsuarioWriteRepository`.
3. **Runtime READ:**
   - Usa entidades y proyecciones (DTOs planos) diseñadas solo para recuperar los datos exactos que requiere la vista, sin mapeos perezosos (`FetchType.LAZY`) innecesarios y sin tracking de cambios de Hibernate.
   - **Ejemplos el proyecto:** `UsuarioReadJpaEntity`, `UsuarioReadProjection`, `ConsultarUsuariosReadProjection`, `UsuarioReadRepository`.
