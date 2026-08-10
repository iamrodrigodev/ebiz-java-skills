---
name: errores-y-excepciones-de-negocio
description: "Excepciones que modelan quiebres de reglas de negocio."
---

# Errores y Excepciones de Negocio

Si un caso de uso falla porque se rompe una regla de negocio o porque los datos solicitados no existen, la capa de Application lanza una Excepción Tipada que representa ese error semántico.

**Diseño de Excepciones:**
- Se crean en el paquete de Application (en sus respectivos runtimes READ/WRITE).
- Extienden de `RuntimeException`.

**Ejemplos de Excepciones WRITE en el proyecto:**
- `CorreoElectronicoYaRegistradoException`
- `PersonaNoExisteException`
- `UsuarioNoExisteException`

**Ejemplos de Excepciones READ en el proyecto:**
- `UsuarioNoExisteException`

**[ADVERTENCIA] Regla de Colisión (Observación de Consolidación):**
Nota que `UsuarioNoExisteException` existe tanto en `[proyecto]-application-read` como en `[proyecto]-application-write`. Debido a que READ y WRITE están separados físicamente (CQRS), esto no causa error en ejecución. Pero es indispensable asegurarse de que los *packages FQCN* sean diferentes (ej. `com.empresa.[proyecto].application.read.exceptions` vs `com.empresa.[proyecto].application.write.exceptions`) para evitar conflictos al cargar dependencias comunes.
