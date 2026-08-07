---
name: cross-cutting-domain
description: Guía para aislar dominios transversales como la Auditoría mediante clases abstractas.
---

# Guía de Implementación: Dominios Transversales (Auditoría)

Los datos transversales como auditoría (quién lo creó, cuándo, etc.) no deben contaminar el código puro de la entidad de negocio. La **Decisión de Arquitectura** dicta crear una clase abstracta `EntidadAuditada`.

## 1. Value Object Transversal
Primero se crea el tipado fuerte para el actor.

```java
package pe.com.mcalderon.logistica.shared.gestionauditoria.domain.model;

public final class ActorAuditoriaId {
    private final Long valor;
    public ActorAuditoriaId(Long valor) {
        if (valor == null) throw new IllegalArgumentException("El identificador del actor de auditoría no puede ser nulo");
        if (valor <= 0) throw new IllegalArgumentException("El identificador del actor de auditoría debe ser mayor que cero");
        this.valor = valor;
    }
    public Long valor() { return valor; }
}
```

## 2. La Clase Abstracta `EntidadAuditada`

Todas las entidades de negocio que requieran auditoría heredarán de esta clase. Los métodos modificadores de auditoría son `protected` para que solo la entidad hija pueda registrar cambios.

```java
package pe.com.mcalderon.logistica.shared.gestionauditoria.domain.model;

import java.time.LocalDateTime;

public abstract class EntidadAuditada {
    private boolean estadoRegistro;
    private LocalDateTime fechaCreacion;
    private ActorAuditoriaId actorCreacionId;
    private LocalDateTime fechaModificacion;
    private ActorAuditoriaId actorModificacionId;

    // Constructor para nacimiento
    protected EntidadAuditada(LocalDateTime fechaCreacion, ActorAuditoriaId actorCreacionId) {
        if (fechaCreacion == null) throw new IllegalArgumentException("La fecha de creación no puede ser nula");
        if (actorCreacionId == null) throw new IllegalArgumentException("El actor de creación no puede ser nulo");
        this.estadoRegistro = true;
        this.fechaCreacion = fechaCreacion;
        this.actorCreacionId = actorCreacionId;
        this.fechaModificacion = null;
        this.actorModificacionId = null;
    }

    // Métodos protected para cambiar el estado (Auditoría se valida ANTES del negocio)
    protected void registrarModificacion(LocalDateTime fechaModificacion, ActorAuditoriaId actorModificacionId) {
        if (fechaModificacion == null) throw new IllegalArgumentException("La fecha de modificación no puede ser nula");
        if (actorModificacionId == null) throw new IllegalArgumentException("El actor de modificación no puede ser nulo");
        if (fechaModificacion.isBefore(fechaCreacion)) {
            throw new IllegalArgumentException("La fecha de modificación no puede ser anterior a la fecha de creación");
        }
        this.fechaModificacion = fechaModificacion;
        this.actorModificacionId = actorModificacionId;
    }

    protected void eliminarRegistro(LocalDateTime fechaModificacion, ActorAuditoriaId actorModificacionId) {
        registrarModificacion(fechaModificacion, actorModificacionId);
        this.estadoRegistro = false;
    }
    
    // Getters públicos para estadoRegistro(), fechaCreacion(), etc.
}
```

## Instrucciones para el Agente
- La entidad de negocio (ej. `Usuario`) hace `extends EntidadAuditada`.
- En sus métodos de negocio (`darDeBaja`), el usuario primero llama a `registrarModificacion()` para asegurar la consistencia antes de aplicar su lógica propia.
