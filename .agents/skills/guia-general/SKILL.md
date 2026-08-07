---
name: guia-general
description: Indice oficial de reglas de Arquitectura Hexagonal y DDD.
---

# Guia General de Arquitectura Hexagonal y DDD

**Objetivo:** Servir como base de conocimiento para comprender la estructura y reglas de este repositorio, el cual define el estandar de programacion del equipo.

## Estructura de la Documentacion

La documentacion esta fragmentada en modulos especificos. A continuacion, el indice de todas las guias arquitectonicas que debes seguir obligatoriamente durante el desarrollo:

### 00. Flujo de Trabajo
*   **`build-feature`**: Orquestador paso a paso. Muestra en que orden se construyen las capas de una feature.

### 01. Fundamentos
*   **`architecture-review`**: Reglas de aislamiento e importaciones.
*   **`dependency-rules`**: Flujo correcto de dependencias entre capas.
*   **`package-structure`**: Vertical Slicing.

### 02. Dominio Puro (El Corazon del Negocio)
*   **`domain-model`**: Entidades ricas, constructores privados e invariantes.
*   **`value-objects`**: Eliminacion de primitivos.
*   **`cross-cutting-domain`**: Auditoria via `EntidadAuditada`.

### 03. Capa de Aplicacion (La Orquestacion)
*   **`ports`**: Puertos de Entrada y Salida.
*   **`usecases`**: Servicios de Aplicacion puros.
*   **`transactions`**: Reglas sobre `@Transactional`.

### 04. Capa de Infraestructura (La Frontera)
*   **`rest-api`**: Input Adapters (Controladores).
*   **`rest-mappers`**: Aislamiento DTO <-> Domain.
*   **`global-exception-handler`**: Captura y estandarizacion de errores HTTP.
*   **`persistence-entities`**: Entidades exclusivas para Hibernate.
*   **`persistence-repositories`**: Interfaces Spring Data.
*   **`persistence-mappers`**: Traduccion JPA <-> Domain.
*   **`persistence-adapters`**: Output Adapters.
*   **`sql-server-config`**: Config de Base de Datos.

### 05. Testing
*   **`domain-testing`**: JUnit 5 puro.
*   **`application-testing`**: Implementacion mediante Fakes (sin Mockito).
*   **`e2e-testing`**: Pruebas de integracion con MockMvc.

### 06. Librerias y Ecosistema
*   **`lombok`**: Reglas y restricciones de uso.
*   **`spring-ecosystem`**: Integracion de dependencias.
*   **`validation`**: Reglas para Jakarta Validation.

## Instrucciones Especificas para Agentes IA

- Antes de escribir cualquier fragmento de codigo o diseñar una arquitectura, debes consultar la skill individual de esta lista que corresponda a la tarea.
- Si el humano te pide documentacion o ejemplos sobre "Como programamos aqui", debes mostrarle el contenido de la skill especifica en lugar de inventar reglas o usar comandos externos genéricos.
