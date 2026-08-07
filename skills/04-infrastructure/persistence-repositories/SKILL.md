---
name: persistence-repositories
description: Guía oficial del equipo para la creación de Repositorios (JpaRepository) y consultas a base de datos.
---

# Guía de Implementación: Repositorios (JPA)

Esta guía establece el estándar para consultar y guardar datos mediante `JpaRepository` en la arquitectura del proyecto.

## Reglas de Implementación

### 1. El Puerto de Salida es el JpaRepository
Por convención del equipo, el puerto de salida de la aplicación es directamente la interfaz de Spring Data JPA. Esto elimina la necesidad de crear adaptadores intermediarios innecesarios.

### 2. Ejemplo de Implementación (Patrón Oficial)

```java
package com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.repository;

import com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.entity.UsuarioJpaEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;

@Repository
public interface UsuarioRepository extends JpaRepository<UsuarioJpaEntity, String> {
    
    // 1. Query Methods de Spring Data (Preferidos para consultas simples)
    Optional<UsuarioJpaEntity> findByCorreo(String correo);
    
    boolean existsByCorreo(String correo);
    
    List<UsuarioJpaEntity> findByEstado(String estado);

    // 2. JPQL (Para consultas un poco más complejas)
    @Query("SELECT u FROM UsuarioJpaEntity u WHERE u.estado = :estado AND u.fechaCreacion > CURRENT_DATE")
    List<UsuarioJpaEntity> findUsuariosNuevosPorEstado(@Param("estado") String estado);
    
    // 3. Consultas Nativas (Usar SOLO cuando se requiere una optimización específica de la BD)
    @Query(value = "SELECT * FROM usuarios WHERE correo LIKE %:dominio%", nativeQuery = true)
    List<UsuarioJpaEntity> findByDominioDeCorreoNative(@Param("dominio") String dominio);
}
```

## Instrucciones para el Agente (LLM)
- Prioriza siempre los *Query Methods* automáticos de Spring Data JPA (`findByX`).
- Si la consulta es compleja (múltiples JOINs que afectan el rendimiento), utiliza JPQL con la anotación `@Query`.
- Usa `@Query(nativeQuery = true)` exclusivamente como último recurso si hay sintaxis propia del motor (PostgreSQL, MySQL, etc.) que no se pueda replicar en JPQL.

