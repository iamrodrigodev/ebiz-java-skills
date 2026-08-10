---
name: persistencia-aislada
description: Aislamiento de entidades JPA.
---

# Persistencia Separada
- `UsuarioReadJpaEntity` y `UsuarioJpaEntity` no se comparten.
- La persistencia READ usa su propio modelo/proyección para evitar bloqueos y cargas innecesarias con la escritura.
