---
name: cqrs-fisico
description: "Separación absoluta y física de los flujos de lectura y escritura."
---

# CQRS Físico (Command Query Responsibility Segregation)

**Definición:**
En esta arquitectura, READ (lecturas) y WRITE (escrituras) no solo están separados a nivel de clases, sino a nivel FÍSICO (artefactos Maven y Runtimes separados).

**Reglas Inquebrantables:**
1. **[OBLIGATORIO] Separación Física:** `Application` está físicamente separada en `[proyecto]-application-read` y `[proyecto]-application-write`.
2. **[OBLIGATORIO] Artefactos Independientes:** READ y WRITE son artefactos y procesos completamente independientes. Cada lado tiene su propio runtime Spring Boot desplegable.
3. **[PROHIBIDO] Dependencias Cruzadas:** 
   - La parte READ **NUNCA** depende de `application-write` ni de `[proyecto]-write`.
   - La parte WRITE **NUNCA** depende de `application-read` ni de `[proyecto]-read`.

**Evidencia en el Proyecto el proyecto:**
- `[proyecto]-read` levanta en el puerto 8081 y es un microservicio autónomo.
- `[proyecto]-write` levanta en el puerto 8082 y es otro microservicio autónomo.
