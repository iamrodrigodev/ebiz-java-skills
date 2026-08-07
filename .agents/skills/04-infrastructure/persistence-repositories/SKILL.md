---
name: persistence-repositories
description: Guia de implementacion para Interfaces JpaRepository de Spring Data.
---

# Guia de Repositorios JPA

**Objetivo:** Proveer la interfaz estandar de acceso a datos utilizando el framework Spring Data JPA. Su unico rol es dialogar con la tabla fisica usando `UsuarioJpaEntity`.

## Reglas de Implementacion y Arquitectura

1. Las interfaces de repositorios no implementan los Casos de Uso.
2. Todo repositorio hereda de `JpaRepository` usando exclusivamente las entidades JPA, NUNCA las entidades puras del dominio.
3. El repositorio vive y muere en la capa de `infrastructure`.

## Lo que SI debes hacer (Buenas Practicas)

```java
package pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.adapter.out.persistence.repository;

import org.springframework.data.jpa.repository.JpaRepository;

// Utiliza la entidad JPA (UsuarioJpaEntity) y su tipo de ID primitivo (Long)
public interface UsuarioJpaRepository extends JpaRepository<UsuarioJpaEntity, Long> {
    
    // Nombres de metodos nativos de Spring Data
    boolean existsByCorreo(String correo);
    
    boolean existsByCorreoAndUsuarioIdNot(String correo, Long usuarioId);
}
```

## Lo que NO debes hacer (Anti-patrones)

```java
// ERROR CATASTROFICO: Usar la entidad del dominio puro (Usuario) en el repositorio
public interface UsuarioJpaRepository extends JpaRepository<Usuario, UsuarioId> { ... }
```

## Instrucciones Especificas para Agentes IA

- Nombrar siempre la clase como `[NombreEntidad]JpaRepository`.
- NUNCA usar la notacion genérica `<Usuario, Long>`, SIEMPRE usar `<UsuarioJpaEntity, Long>`.
