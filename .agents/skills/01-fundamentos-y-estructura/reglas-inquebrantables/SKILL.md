---
name: reglas-inquebrantables
description: "Los 9 principios base del proyecto."
---

# Los 9 Principios que NO Deben Romperse

Este es el manifiesto arquitectónico. Si alguna de estas reglas se rompe, el PR debe ser rechazado inmediatamente.

1. **Dominio primero:** El dominio no depende de Spring, JPA, REST ni Application.
2. **Application pura:** Handlers, Commands/Queries y Ports no llevan anotaciones Spring/JPA.
3. **Dependencia hacia adentro:** Los adapters implementan Port-Out; Application nunca conoce repositories directamente.
4. **Vertical Slice:** Cada capability se agrupa por intención de negocio (`crearusuario`, `modificarusuario`).
5. **Screaming Architecture:** Los nombres y packages deben revelar capacidades del negocio, no sólo tecnología.
6. **CQRS físico:** READ y WRITE son artefactos y procesos independientes.
7. **Transacción en infraestructura:** `@Transactional` se aplica mediante Decorators en Infraestructura, no sobre Handlers de Application.
8. **Persistencia separada:** Las entidades JPA son distintas. `UsuarioReadJpaEntity` y `UsuarioJpaEntity` no se comparten jamás.
9. **Guardrails ejecutables:** ArchUnit protege las capas/slices y Maven Enforcer impide las dependencias cruzadas entre READ y WRITE.
