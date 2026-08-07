---
name: package-structure
description: Estandar de la estructura de paquetes para Vertical Slicing.
---

# Guia de Estructura de Paquetes

**Objetivo:** Estandarizar la organizacion fisica de los archivos del proyecto utilizando el enfoque Screaming Architecture combinado con Vertical Slicing. La arquitectura debe expresar claramente las capacidades del negocio, no los frameworks tecnologicos.

## Reglas de Implementacion y Arquitectura

1. La organizacion debe ser por "Bounded Contexts" (Vertical Slicing), donde cada capacidad funcional vive de manera autonoma.
2. Cada capacidad funcional (ej. `gestionusuarios`, `gestionpersonas`, `gestionauditoria`) debe contener internamente las tres carpetas base: `domain`, `application` e `infrastructure`.
3. Nunca crear paquetes transversales tecnicos globales como `controllers`, `services` o `entities` en la raiz del proyecto.

## Lo que SI debes hacer (Buenas Practicas)

```text
pe.com.mcalderon.logistica
└── identidades
    └── gestionusuarios
        ├── domain
        │   ├── exception
        │   ├── model
        │   └── service
        ├── application
        │   ├── command
        │   ├── exception
        │   ├── port
        │   │   ├── in
        │   │   └── out
        │   ├── result
        │   └── service
        └── infrastructure
            ├── adapter
            │   ├── in
            │   │   └── rest
            │   └── out
            │       └── persistence
            └── config
```

## Lo que NO debes hacer (Anti-patrones)

```text
// ARQUITECTURA BASADA EN TECNOLOGIA (PROHIBIDA)
pe.com.mcalderon.logistica
├── controllers
│   ├── UsuarioController.java
│   └── PersonaController.java
├── services
│   ├── UsuarioService.java
│   └── PersonaService.java
└── repositories
    ├── UsuarioRepository.java
    └── PersonaRepository.java
```

## Instrucciones Especificas para Agentes IA

- Cuando vayas a generar un nuevo componente, primero identifica el modulo vertical al que pertenece.
- La creacion de cualquier archivo nuevo debe ubicarse dentro de las estructuras internas de capa (`domain/model`, `infrastructure/adapter/in/rest`, etc.).
- Respeta estrictamente los nombres de los directorios: `domain` (no dominio), `application` (no aplicacion), `infrastructure` (no infraestructura).
