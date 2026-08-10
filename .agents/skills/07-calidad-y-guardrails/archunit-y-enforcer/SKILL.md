---
name: archunit-y-enforcer
description: "Guardrails automatizados para proteger fronteras CQRS."
---

# ArchUnit y Maven Enforcer (Guardianes Ejecutables)

El proyecto cuenta con un módulo transversal llamado `[proyecto]-architecture-tests`. 
Este módulo tiene un rol crucial: Automatizar las reglas de arquitectura para que los desarrolladores no rompan el patrón CQRS ni el Hexágono de pura casualidad.

**ArchUnit (Librería de pruebas de arquitectura):**
- Aserciones que escanean el classpath y verifican que ninguna clase en el paquete `domain` importe algo del paquete `application` o `infrastructure`.
- Vigila que se respete el Vertical Slicing (que un capability no llame a otro incorrectamente).

**Maven Enforcer Plugin (Capa Física):**
- Es una configuración a nivel del POM (`pom.xml`).
- **El Bloqueo:** Está configurado para disparar un error catastrófico (`BUILD FAILURE`) si el módulo `[proyecto]-read` (Infraestructura de lectura) intenta declarar una dependencia física sobre `[proyecto]-write` o `[proyecto]-application-write`, impidiendo que los runtimes se contaminen.

**Nota de Refactor:** Se identificó que existía una clase `[proyecto]-architecture-tests/src/Main.java`. Esta clase no aporta a la arquitectura y debería borrarse para dejar el módulo solo para pruebas.
