---
name: archunit-y-enforcer
description: Reglas transversales de protección.
---

# Guardrails Ejecutables
- **ArchUnit:** Protege que las capas de Dominio y Application no accedan a Infraestructura, y vigila los Slices. Los tests de arquitectura se ejecutan en un módulo transversal (`logistica-architecture-tests`).
- **Maven Enforcer:** Impide dependencias cruzadas entre artefactos físicos READ <-> WRITE.
