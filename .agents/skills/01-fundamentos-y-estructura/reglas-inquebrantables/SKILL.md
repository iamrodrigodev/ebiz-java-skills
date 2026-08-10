---
name: reglas-inquebrantables
description: Los 9 principios base del proyecto.
---

# Principios que no deben romperse
1. Dominio primero.
2. Application pura (sin Spring/JPA).
3. Dependencia hacia adentro (Adapters a Port-Out).
4. Vertical Slice (por intención).
5. Screaming Architecture.
6. CQRS Físico.
7. Transacción en infraestructura (decorators).
8. Persistencia separada (UsuarioReadJpaEntity != UsuarioJpaEntity).
9. Guardrails ejecutables (ArchUnit, Maven Enforcer).
