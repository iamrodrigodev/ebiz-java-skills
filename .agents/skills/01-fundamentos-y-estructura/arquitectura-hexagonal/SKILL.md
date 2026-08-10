---
name: arquitectura-hexagonal
description: Aislamiento del dominio y uso de puertos.
---

# Arquitectura Hexagonal
- **Dominio primero:** El dominio no depende de Spring, JPA, REST ni Application.
- **Dependencia hacia adentro:** Los adapters implementan Port-Out; Application nunca conoce repositories.
