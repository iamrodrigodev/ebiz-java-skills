---
name: dependency-rules
description: Instrucciones para auditar las reglas de dependencia, contemplando las excepciones del equipo.
---

# Guía de Revisión: Reglas de Dependencia

El agente o desarrollador debe revisar el código fuente e identificar si existen violaciones a las dependencias.

## 1. Reglas para la capa `domain` (ESTRICTA)
El dominio NO debe depender de nada externo.

- **Imports Permitidos:** `java.util.*`, `java.time.*`, paquetes del propio dominio.
- **Imports Prohibidos (Error Crítico):** 
  `org.springframework.*`, `jakarta.persistence.*`, `infrastructure.*`, `application.*`.

## 2. Reglas para la capa `application` (FLEXIBILIZADA)
La aplicación conoce al dominio. 
*Nota del equipo:* Como la "Regla del equipo" dicta el uso de JPA, es posible que los Casos de Uso importen las interfaces `JpaRepository` directamente desde la infrastructure si el equipo ha decidido inyectar los repositorios de Spring Data directo en los servicios de aplicación.

- **Imports Permitidos:**
  Paquetes de `domain`, y bajo la regla del equipo, paquetes de los repositorios de JPA si se inyectan en los Casos de Uso.
- **Imports Prohibidos:**
  Lógica de controladores web (`org.springframework.web.*`).

## 3. Reglas para la capa `infrastructure`
Es la capa donde residen los frameworks.

- **Imports Permitidos:**
  Todo lo relacionado a Spring Boot, JPA, Web, etc. Conoce a `application` y `domain`.

