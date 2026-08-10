---
name: checklist-aceptacion
description: Checklist obligatorio antes de cerrar tarea.
---

# Checklist de Aceptación Arquitectónica
[ ] ¿El dominio compila sin Spring/JPA?
[ ] ¿Application compila sin infraestructura/Spring/JPA?
[ ] ¿El Controller sólo orquesta protocolo y llama un Port-In?
[ ] ¿El Handler sólo usa dominio y Ports?
[ ] ¿Los Port-Out están definidos por necesidad del caso de uso?
[ ] ¿La transacción vive en un decorator de infraestructura?
[ ] ¿READ y WRITE son física y lógicamente independientes?
[ ] ¿La persistencia READ usa su propio modelo/proyección?
[ ] ¿mvn clean verify termina verde?
[ ] ¿Los dos runtimes pueden levantarse simultáneamente por 8081/8082?
