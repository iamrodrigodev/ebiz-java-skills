---
name: checklist-aceptacion
description: "Las 11 preguntas que debes responder antes de cerrar una tarea."
---

# Checklist de Aceptación Arquitectónica

Este es el Golden Path. Antes de considerar que un código estructural está terminado, como Agente de IA, DEBES validar mentalmente cada punto de esta lista:

- [ ] ¿El dominio compila sin Spring/JPA?
- [ ] ¿Application compila sin infraestructura/Spring/JPA?
- [ ] ¿El Controller sólo orquesta protocolo y llama un Port-In?
- [ ] ¿El Handler sólo usa dominio y Ports?
- [ ] ¿Los Port-Out están definidos por necesidad del caso de uso y no por el Repository existente?
- [ ] ¿La transacción vive en un decorator de infraestructura?
- [ ] ¿READ no depende de application-write ni de [proyecto]-write?
- [ ] ¿WRITE no depende de application-read ni de [proyecto]-read?
- [ ] ¿La persistencia READ usa su propio modelo/proyección?
- [ ] ¿mvn clean verify termina en verde?
- [ ] ¿Los dos runtimes pueden levantarse simultáneamente por los puertos 8081 y 8082?
