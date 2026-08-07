---
name: architecture-review
description: Guía de revisión para auditar un módulo y garantizar que cumple con la Arquitectura Hexagonal y DDD.
---

# Guía de Revisión de Arquitectura

Este documento sirve como manual estricto tanto para desarrolladores del equipo como para agentes de Inteligencia Artificial. Su propósito es definir cómo auditar el código fuente para garantizar que no existan violaciones a los principios de Arquitectura Hexagonal, Clean Architecture y Domain-Driven Design.

## Instrucciones de Revisión (Paso a Paso)

Al revisar un módulo o funcionalidad, se deben ejecutar los siguientes pasos de forma metódica:

1. **Analizar la Estructura (Vertical Slicing):**
   - El módulo debe estar dividido obligatoriamente en las carpetas `domain`, `application` e `infrastructure`.
   - Si falta alguna capa o existen carpetas globales fuera de lugar (ej. un paquete `controllers` en la raíz), debe reportarse como error.

2. **Auditar la Capa de Dominio (`domain`):**
   - Revisa todos los archivos dentro de `domain/model`, `domain/service`, `domain/exception`.
   - **Regla Estricta:** Revisa todos los bloques `import`. Está TOTALMENTE PROHIBIDO importar cualquier clase que pertenezca a `org.springframework.*`, `jakarta.persistence.*`, u otros frameworks (con excepción de anotaciones estándar de validación si el equipo lo permite).
   - **Regla de Dominio Rico:** Verifica que las clases en `model` tengan métodos que reflejen acciones de negocio (ej. `publicar()`, `desactivar()`) y no solo getters/setters anémicos.

3. **Auditar la Capa de Aplicación (`application`):**
   - Revisa las clases en `application/service`.
   - **Regla Estricta:** Solo pueden importar clases de `application` y `domain`. No pueden importar nada de `infrastructure`.
   - Verifica que los casos de uso implementen una interfaz (`port.in`) y usen interfaces para comunicarse hacia afuera (`port.out`).

4. **Auditar la Capa de infrastructure (`infrastructure`):**
   - Revisa los controladores en `adapter.in.rest` y repositorios en `adapter.out.persistence`.
   - Verifica que los controladores llamen a las interfaces de los casos de uso (`port.in`) y no directamente al dominio ni a las bases de datos.

## Ejemplos de Estructura Esperada

Para dar contexto a los desarrolladores y agentes, esta es la estructura correcta que se debe exigir:

```text
com.empresa.logistica.gestionusuarios
├── domain
│   ├── model       (Entidades puras)
│   ├── exception   (Errores de negocio)
│   └── service     (Lógica que cruza varias entidades)
├── application
│   ├── port
│   │   ├── in      (Interfaces de casos de uso)
│   │   └── out     (Interfaces hacia bases de datos/externos)
│   ├── command     (DTOs de entrada a los casos de uso)
│   └── service     (Implementación de port.in)
└── infrastructure
    ├── adapter
    │   ├── in
    │   │   └── rest             (Spring @RestController)
    │   └── out
    │       └── persistence      (Spring @Repository y Entidades JPA)
    └── config                   (Spring @Configuration)
```

## Formato de Reporte Esperado
Tras finalizar la auditoría (ya sea humana o automatizada), se debe generar un reporte que contenga:
- **Aprobado**: Archivos que cumplen las reglas perfectamente.
- **Violaciones de Arquitectura**: Lista detallada de archivos, líneas exactas y el motivo por el cual rompen las reglas.
- **Plan de Refactorización**: Sugerencias de código para solucionar los problemas encontrados.

