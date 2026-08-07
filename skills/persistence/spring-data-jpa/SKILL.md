---
name: spring-data-jpa
description: Guía oficial del equipo para el uso de Spring Data JPA y el modelado de Entidades de Base de Datos.
---

# Guía de Implementación: Spring Data JPA (Entidades)

Esta guía define las reglas de persistencia para desarrolladores y agentes de IA. La regla del equipo dicta el uso exclusivo de **Spring Data JPA** para el acceso a datos.

## Reglas de Mapeo de Entidades JPA

Las entidades de base de datos (`@Entity`) viven exclusivamente en la capa de `infrastructure` y sirven únicamente como un reflejo de las tablas relacionales. **No deben contener lógica de negocio.**

### 1. Ubicación y Nomenclatura
- **Ruta:** `infrastructure/adapter/out/persistence/entity/`
- **Sufijo:** Se recomienda usar el sufijo `JpaEntity` o `Entity` (ej. `UsuarioJpaEntity`) para evitar colisiones de nombre con el Dominio Rico (`Usuario`).

### 2. Ejemplo de Implementación (Patrón Oficial)

```java
package com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.entity;

import jakarta.persistence.*;
import lombok.Getter;
import lombok.Setter;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import java.time.LocalDateTime;

// REGLA: Usamos Lombok para evitar boilerplate de getters/setters, pero evitamos @Data
// porque puede causar problemas de rendimiento con equals() y hashCode() en JPA.
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Table(name = "usuarios")
public class UsuarioJpaEntity {

    @Id
    @Column(name = "id_usuario", length = 36, nullable = false)
    private String id;
    
    @Column(name = "correo", nullable = false, unique = true, length = 100)
    private String correo;
    
    @Column(name = "estado", nullable = false, length = 30)
    private String estado;
    
    @Column(name = "fecha_creacion", nullable = false, updatable = false)
    private LocalDateTime fechaCreacion;
}
```

## Instrucciones para el Agente (LLM)
- Nunca añadas lógica de negocio (validaciones complejas, cálculo de estados) dentro de estas entidades. Todo eso pertenece al `Domain Model`.
- Evita usar la anotación `@Data` de Lombok en entidades JPA. Usa `@Getter` y `@Setter`.

