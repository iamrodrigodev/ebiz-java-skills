---
name: domain-model
description: Guia estricta para la construccion de Entidades de Dominio rico.
---

# Guia de Modelado de Dominio Rico

**Objetivo:** Desarrollar agregados y entidades de negocio puras, que protejan sus propias invariantes, mantengan el estado interno completamente encapsulado, y expongan solo operaciones semanticas de negocio, evitando el anti-patron de entidades anemicas.

## Reglas de Implementacion y Arquitectura

1. **Constructores de Nacimiento:** Los constructores de inicializacion deben ser declarados como `private` (o `protected`).
2. **Fabricas Estaticas Explicitas:** Toda instanciacion debe ocurrir mediante fabricas estaticas (`public static Entity crear(...)` o `public static Entity rehidratar(...)`).
3. **Validacion de Invariantes Iniciales:** Todo parametro nulo o estado incoherente al momento de la creacion debe lanzar `IllegalArgumentException` o `IllegalStateException`.
4. **Cero Setters Publicos:** El cambio de estado ocurre exclusivamente mediante operaciones de negocio (`darDeBaja`, `activar`, `renovarVigencia`).

## Lo que SI debes hacer (Buenas Practicas)

```java
public final class Usuario extends EntidadAuditada {
    private final UsuarioId id;
    private EstadoCuenta estadoCuenta;

    // 1. Constructor privado
    private Usuario(UsuarioId id, EstadoCuenta estadoCuenta, LocalDateTime fechaCreacion, ActorAuditoriaId actor) {
        super(fechaCreacion, actor);
        this.id = id;
        this.estadoCuenta = estadoCuenta;
    }

    // 2. Fabrica explicita para crear un usuario desde cero
    public static Usuario crear(UsuarioId id, LocalDateTime fechaCreacion, ActorAuditoriaId actor) {
        if (id == null) throw new IllegalArgumentException("ID nulo");
        // El estado inicial lo decide el dominio, no la capa de aplicacion
        return new Usuario(id, EstadoCuenta.ACTIVACION_PENDIENTE, fechaCreacion, actor);
    }

    // 3. Fabrica explicita para reconstruir la entidad (usada por repositorios)
    public static Usuario rehidratar(UsuarioId id, EstadoCuenta estadoCuenta, LocalDateTime fechaCreacion, ActorAuditoriaId actor) {
        return new Usuario(id, estadoCuenta, fechaCreacion, actor);
    }

    // 4. Operaciones de negocio, no setters
    public void activar() {
        if (this.estadoCuenta != EstadoCuenta.ACTIVACION_PENDIENTE) {
            throw new IllegalStateException("El usuario no esta en estado para ser activado");
        }
        this.estadoCuenta = EstadoCuenta.ACTIVO;
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Exponer propiedades de la clase, constructores publicos o mutadores genericos.

```java
// ESTO ES UN MODELO ANEMICO PROHIBIDO EN ESTE EQUIPO
public class Usuario {
    public Long id;
    public String estado;

    public Usuario() {}

    public void setEstado(String estado) {
        this.estado = estado; // El dominio no verifica si la transicion es valida
    }
}
```

## Instrucciones Especificas para Agentes IA

- Esta terminantemente prohibido utilizar las anotaciones `@Data`, `@NoArgsConstructor` o `@AllArgsConstructor` de Lombok en el paquete `domain/model`.
- Si el usuario te pide crear una entidad, debes generar el constructor privado, la fabrica estatica `crear()`, la fabrica estatica `rehidratar()` y los metodos mutadores de estado basados en verbos del negocio.
