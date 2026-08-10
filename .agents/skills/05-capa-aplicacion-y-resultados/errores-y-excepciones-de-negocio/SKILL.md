---
name: errores-y-excepciones-de-negocio
description: Excepciones lanzadas por reglas de negocio.
---

# Excepciones de Application
- Crear excepciones de Application cuando la condición pertenezca al caso de uso (ej. `UsuarioNoExisteException`).
- Cuidado con nombres duplicados entre read y write. Deben diferenciarse por package.
