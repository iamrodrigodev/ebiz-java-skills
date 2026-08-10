---
name: cqrs-fisico
description: Separación de lecturas y escrituras.
---

# CQRS Físico
READ y WRITE son artefactos y procesos independientes.
- Application está físicamente separada en READ y WRITE.
- Cada lado tiene su propio runtime Spring Boot desplegable.
