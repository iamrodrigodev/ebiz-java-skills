---
name: guia-general
description: Indice maestro de la arquitectura Logistica CQRS Hexagonal.
---

# Guía General de Arquitectura Logística

**Objetivo:** Este es el punto de partida. La arquitectura se basa en Vertical Slicing, Arquitectura Hexagonal y CQRS Físico.

## Índice de Conocimiento

### 01-fundamentos-y-estructura
Reglas conceptuales de diseño, slicing por intención y nombres que revelan el negocio.
### 02-baseline-fisica
La organización en módulos Maven (`logistica-domain`, `logistica-read`, etc.) y el arranque aislado.
### 03-flujos-de-creacion
El paso a paso exacto para crear Capabilities WRITE (11 pasos) y READ (12 pasos). ¡Lee esto antes de programar!
### 04-capa-dominio-y-reglas
Invariantes, políticas, EntidadAuditada y prohibición de setters/constructores vacíos.
### 05-capa-aplicacion-y-resultados
Pureza de Handlers (sin Spring), Puertos, Results y Excepciones de negocio.
### 06-capa-infraestructura-y-http
JPA aislado por runtime, transacciones vía decorators, DTOs REST, validaciones Jakarta y manejo global de errores.
### 07-calidad-y-guardrails
Pruebas aisladas, ArchUnit, Maven Enforcer y checklist de aceptación.

