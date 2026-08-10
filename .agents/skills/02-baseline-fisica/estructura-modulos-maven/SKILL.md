---
name: estructura-modulos-maven
description: Fronteras entre módulos Maven.
---

# Baseline Arquitectónica (Módulos Maven)
- `logistica-domain` — dominio puro y compartido. 
- `logistica-application-read` — queries, Port-In, Port-Out, handlers y resultados de lectura. 
- `logistica-application-write` — commands, Port-In, Port-Out, handlers y excepciones de escritura. 
- `logistica-read` — adapters, JPA READ, transacciones, REST y bootstrap :8081. 
- `logistica-write` — adapters, JPA WRITE, transacciones, REST y bootstrap :8082. 
- `logistica-architecture-tests` — ArchUnit y guardrails transversales. 
- `database` — scripts de base de datos.
