---
name: value-objects-y-enums
description: "Erradicación de primitivos mediante Value Objects y uso de Enums de dominio."
---

# Value Objects y Enums (Erradicación de primitivos)

El "Primitive Obsession" es un antipatrón donde se usan strings, enteros o booleanos en lugar de objetos tipados para representar conceptos del dominio. En este proyecto se prohíbe obsesionarse con primitivos en el corazón del negocio.

**Value Objects (Objetos Inmutables sin Identidad Única):**
- **Identidad Fuerte:** En lugar de `Long usuarioId`, usamos un objeto propio. 
  - **Ejemplos Reales:** `ActorAuditoriaId`, `PersonaId`, `UsuarioId`.
- **Reglas con Invariantes:** Si un concepto tiene reglas de formato, se encapsula. Un correo no es cualquier string.
  - **Ejemplos Reales:** `CorreoElectronico`, `PeriodoVigencia`.

**Enums de Dominio:**
- Si un concepto tiene un universo finito y cerrado de opciones, usar Enums propios del Dominio, jamás Strings sueltos.
- **Ejemplos Reales:** `EstadoCuenta`, `OrigenUsuario`, `TipoCuenta`.

**Regla de Oro:** 
- Todo Value Object es inmutable. Si necesitas cambiarlo, devuelves una nueva instancia. No tienen setters.
