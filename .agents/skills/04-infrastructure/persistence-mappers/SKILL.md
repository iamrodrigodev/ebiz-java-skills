---
name: persistence-mappers
description: Guia para crear Mappers manuales entre el Dominio y Entidades JPA.
---

# Guia de Mappers de Persistencia

**Objetivo:** Implementar el componente tecnico unico encargado de traducir bidireccionalmente el Modelo de Dominio rico y la Entidad JPA de base de datos, evitando que cualquiera de los dos modelos conozca la existencia del otro.

## Reglas de Implementacion y Arquitectura

1. **Aislamiento Total:** El mapper actua como traductor. Toma una Entidad de Dominio y devuelve una `JpaEntity`, o viceversa.
2. **Cero Logica de Negocio:** El mapper NUNCA debe ejecutar validaciones funcionales (ej. validar si una fecha de expiracion ya paso).
3. **Mapeo Manual:** Por convencion arquitectonica del manual base, se evitan herramientas automaticas (como MapStruct) para la persistencia, usando metodos `public final class` con validaciones `Objects.requireNonNull()`.

## Lo que SI debes hacer (Buenas Practicas)

```java
import java.util.Objects;

public final class UsuarioPersistenceMapper {

    // 1. Dominio -> Persistencia
    public UsuarioJpaEntity aJpaEntity(Usuario usuario) {
        Objects.requireNonNull(usuario, "Usuario no puede ser nulo");
        
        return new UsuarioJpaEntity(
            usuario.id() == null ? null : usuario.id().valor(),
            usuario.personaId() == null ? null : usuario.personaId().valor(),
            usuario.nombre(),
            usuario.correo().valor(),
            usuario.origen().codigo(),
            usuario.fechaCreacion()
        );
    }

    // 2. Persistencia -> Dominio
    public Usuario aDominio(UsuarioJpaEntity entity) {
        Objects.requireNonNull(entity, "UsuarioJpaEntity no puede ser nulo");
        
        return Usuario.rehidratar(
            new UsuarioId(entity.usuarioId()),
            new PersonaId(entity.personaId()),
            entity.nombre(),
            new CorreoElectronico(entity.correo()),
            OrigenUsuario.desdeCodigo(entity.origenCodigo()),
            entity.fechaCreacion()
        );
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Usar inyeccion de dependencias dentro del mapper para ir a consultar algo a la base de datos (los mappers no tienen acceso a repositorios).
- Envolver la creacion del dominio dentro de un `try-catch` gigante ignorando las invariantes del objeto real.

## Instrucciones Especificas para Agentes IA

- Siempre debes llamar a la fabrica `rehidratar()` en lugar de `crear()` cuando devuelvas informacion desde la base de datos hacia el dominio, ya que el objeto ya nacio en el pasado y no debe disparar eventos de nacimiento ni validaciones iniciales bloqueantes.
- Utiliza operaciones ternarias o metodos utilitarios nativos para lidiar con posibles nulos en primitivos mapeados desde objetos complejos.
