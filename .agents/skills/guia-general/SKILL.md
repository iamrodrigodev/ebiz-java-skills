---
name: guia-general
description: "Indice maestro de la arquitectura Base CQRS Hexagonal."
---

# Guía General de Arquitectura Base

**Única Fuente de Verdad:**
Este repositorio funciona como la **única fuente de verdad** para el desarrollo de software basado en Arquitectura Hexagonal y Domain-Driven Design (DDD) utilizando Java y Spring Boot. Su propósito es garantizar que todo el equipo (y las herramientas de IA asistida) mantengan un código uniforme, predecible y altamente desacoplado.

**Pilares Arquitectónicos:**
1. **CQRS Físico:** Separación absoluta entre los flujos de lectura y escritura.
2. **Inmunidad ante los cambios tecnológicos (Hexagonal):** El dominio no depende de bases de datos ni frameworks web.
3. **Vertical Slicing:** Las funcionalidades se agrupan por casos de uso completos, no por capas técnicas.
4. **Screaming Architecture:** El código grita el negocio que resuelve, no la tecnología que usa.

**Objetivo:**
Este manual documenta el orden estricto y recomendado para construir una capability (caso de uso) sobre la arquitectura base actual de tu repositorio. 
El objetivo es que un nuevo desarrollo reproduzca las mismas fronteras de Dominio, Application, Infraestructura, CQRS READ/WRITE y Guardrails sin depender de conocimiento tácito del equipo. 
Cada vez que programes, debes respetar esta separación. Nunca inventes estructuras de carpetas o enfoques que rompan con los ejemplos del proyecto.

## ¿Cómo buscar instrucciones?
La documentación está fragmentada por capas y flujos. Busca la habilidad específica que necesitas antes de empezar a programar.
1. **01-fundamentos-y-estructura:** Reglas base de la arquitectura.
2. **02-baseline-fisica:** Módulos de Maven.
3. **03-flujos-de-creacion:** El núcleo del manual. Aquí están los pasos exactos para crear capacidades READ (12 pasos) y WRITE (11 pasos).
4. **04-capa-dominio-y-reglas:** Invariantes y pureza del corazón del negocio.
5. **05-capa-aplicacion-y-resultados:** Excepciones y Handlers sin Spring.
6. **06-capa-infraestructura-y-http:** Adaptadores, JPA aislado y REST.
7. **07-calidad-y-guardrails:** Aseguramiento mediante ArchUnit y Maven Enforcer.

**Regla de Oro:**
Antes de generar código para un `Command`, un `Handler` o un `Repository`, busca en las skills la lista de "Ejemplos Existentes por Capability" y analiza cómo está hecho en el código base (por ejemplo, `CrearUsuarioHandler`).

