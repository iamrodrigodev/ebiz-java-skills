---
name: persistence-mappers
description: Guía oficial del equipo para mapear objetos entre el Dominio Rico y las Entidades JPA.
---

# Guía de Implementación: Mappers (Dominio ↔ JPA)

En este proyecto, el Dominio (Java puro) y la infrastructure (JPA) están separados. Por lo tanto, necesitamos traducir la información cuando los datos entran o salen de la base de datos. Esta guía explica cómo hacerlo.

## Reglas de Mapeo

- Todo mapeo entre `Usuario` (Dominio) y `UsuarioJpaEntity` (JPA) debe ocurrir **antes o después** del Caso de Uso, o encapsularse en una clase/interfaz específica.
- El equipo promueve el uso de constructores manuales, Builders o la librería **MapStruct** para automatizar el proceso sin usar Reflexión lenta.

## Ejemplos de Implementación (Patrón Oficial)

### Opción 1: Mapeo Manual (Recomendado para casos simples)

Crea una clase utilitaria en la infrastructure: `infrastructure/adapter/out/persistence/mapper/UsuarioMapper.java`

```java
package com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.mapper;

import com.empresa.logistica.gestionusuarios.domain.model.Usuario;
import com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.entity.UsuarioJpaEntity;

public class UsuarioMapper {

    // JPA -> DOMINIO (Cuando leemos de la BD)
    public static Usuario toDomain(UsuarioJpaEntity entity) {
        if (entity == null) return null;
        
        // Usamos el constructor o builder de la Entidad de Dominio
        return Usuario.reconstruirDesdeBD(
            entity.getId(), 
            entity.getCorreo(), 
            Usuario.EstadoUsuario.valueOf(entity.getEstado())
        );
    }

    // DOMINIO -> JPA (Cuando guardamos en la BD)
    public static UsuarioJpaEntity toJpaEntity(Usuario domain) {
        if (domain == null) return null;
        
        UsuarioJpaEntity entity = new UsuarioJpaEntity();
        entity.setId(domain.getId());
        entity.setCorreo(domain.getCorreo());
        entity.setEstado(domain.getEstado().name());
        
        return entity;
    }
}
```

### Opción 2: Usando MapStruct (Recomendado para objetos grandes)

```java
package com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.mapper;

import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import com.empresa.logistica.gestionusuarios.domain.model.Usuario;
import com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.entity.UsuarioJpaEntity;

@Mapper(componentModel = "spring")
public interface UsuarioMapStructMapper {

    // Las propiedades se mapean automáticamente por coincidencia de nombres
    Usuario toDomain(UsuarioJpaEntity entity);

    UsuarioJpaEntity toJpaEntity(Usuario domain);
}
```

## Instrucciones para el Agente (LLM)
- Cuando debas mapear, nunca contamines la capa de Dominio con referencias a `UsuarioJpaEntity`. El mapeo ocurre en infrastructure.
- Si el proyecto usa MapStruct, genera la interfaz `@Mapper`. Si no lo especifican, asume un Mapper manual estático.

