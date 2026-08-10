---
name: reglas-de-negocio-e-invariantes
description: Reglas estrictas de pureza del dominio (sin setters/constructores vacios).
---

# Reglas e Invariantes
- Evitar setters públicos genéricos y modelo anémico.
- Los constructores deben proteger las invariantes; no usar `@NoArgsConstructor` de Lombok en el dominio.
