---
name: write-intencion-y-puertos
description: Paso 1, 2 y 3 de WRITE (Command, UseCase, Port-Out).
---

# Creación Capability WRITE (Pasos 1-3)
1. **Command:** Expresa la intención de escritura. No contiene anotaciones HTTP/JPA.
2. **UseCase (Port-In):** Interfaz que expone la operación.
3. **Port-Out:** Interfaces de dependencias externas. Sólo los que el Handler necesita (cargar, guardar, validar).
