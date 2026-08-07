---
name: persistence-adapters
description: Guia de implementacion de Adaptadores de Persistencia para SQL Server.
---

# Guia de Adaptadores de Persistencia

**Objetivo:** Los Adaptadores materializan los Puertos de Salida definidos por la capa de aplicacion, interactuando de forma concreta con los repositorios Spring Data JPA y mapeando entidades fisicas hacia/desde el dominio.

## Reglas de Implementacion y Arquitectura

1. Un unico Adaptador puede implementar multiples Puertos de Salida pequemos relacionados (Interface Segregation Principle).
2. El Adaptador tiene como unica responsabilidad orquestar repositorios tecnicos (`JpaRepository`) y herramientas de traduccion (`Mapper`).
3. El Adaptador jamas debe tener reglas de negocio ni alterar el estado interno de la Entidad de Dominio.

## Lo que SI debes hacer (Buenas Practicas)

```java
import java.util.Objects;
import java.util.Optional;

// El adaptador implementa todos los puertos de salida relacionados al Usuario
public final class UsuarioPersistenceAdapter implements GuardarUsuarioPort, ConsultarUsuarioPorIdPort {

    private final UsuarioJpaRepository repository;
    private final UsuarioPersistenceMapper mapper;

    public UsuarioPersistenceAdapter(UsuarioJpaRepository repository, UsuarioPersistenceMapper mapper) {
        this.repository = Objects.requireNonNull(repository, "Repositorio no puede ser nulo");
        this.mapper = Objects.requireNonNull(mapper, "Mapper no puede ser nulo");
    }

    @Override
    public UsuarioId guardar(Usuario usuario) {
        Objects.requireNonNull(usuario, "Usuario no puede ser nulo");
        
        UsuarioJpaEntity entity = mapper.aJpaEntity(usuario);
        UsuarioJpaEntity entityGuardada = repository.save(entity);
        
        return new UsuarioId(entityGuardada.usuarioId());
    }

    @Override
    public Optional<Usuario> cargarPorId(UsuarioId usuarioId) {
        Objects.requireNonNull(usuarioId, "UsuarioId no puede ser nulo");
        
        return repository.findById(usuarioId.valor()).map(mapper::aDominio);
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Colocar `@Component` o `@Repository` en el Adaptador; al igual que los Application Services, la instanciacion de los adaptadores se inyecta via constructores en las clases `@Configuration`.
- Intentar parsear las fechas o aplicar condiciones de vigencia dentro de `guardar()` o `cargarPorId()`.

## Instrucciones Especificas para Agentes IA

- Siempre que crees un adaptador, inyecta su `JpaRepository` correspondiente y el `Mapper` para traducir entre entidades del Dominio y `JpaEntity`.
- Usa siempre `Objects.requireNonNull()` para proteger las variables del adaptador.
