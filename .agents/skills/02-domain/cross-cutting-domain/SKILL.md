---
name: cross-cutting-domain
description: Guia de gestion de dominios transversales (Auditoria) a nivel arquitectonico.
---

# Guia de Dominios Transversales (Auditoria)

**Objetivo:** Establecer el mecanismo arquitectonico para gestionar responsabilidades que cruzan multiples bounded contexts (como la auditoria de modificacion de datos), garantizando consistencia sin acoplar los modulos principales.

## Reglas de Implementacion y Arquitectura

1. Las reglas transversales de auditoria se controlan mediante herencia en el modelo de dominio.
2. Todas las entidades de dominio que deban ser auditadas extenderan de la clase abstracta `EntidadAuditada`.
3. La entidad de auditoria contiene el estado del registro (activo/inactivo logico) y las trazas de tiempo e identidad (`fechaCreacion`, `actorModificacionId`).
4. Las modificaciones a las fechas y actores de auditoria deben ser invocadas mediante metodos `protected` por la entidad hija antes de completar cualquier operacion de mutacion.

## Lo que SI debes hacer (Buenas Practicas)

```java
// Entidad auditada base
public abstract class EntidadAuditada {
    private boolean estadoRegistro;
    private LocalDateTime fechaCreacion;
    private ActorAuditoriaId actorCreacionId;
    private LocalDateTime fechaModificacion;
    private ActorAuditoriaId actorModificacionId;

    protected EntidadAuditada(LocalDateTime fechaCreacion, ActorAuditoriaId actorCreacionId) {
        if (fechaCreacion == null) throw new IllegalArgumentException("La fecha no puede ser nula");
        this.estadoRegistro = true;
        this.fechaCreacion = fechaCreacion;
        this.actorCreacionId = actorCreacionId;
    }

    protected void registrarModificacion(LocalDateTime fecha, ActorAuditoriaId actor) {
        if (fecha == null) throw new IllegalArgumentException("Fecha no puede ser nula");
        this.fechaModificacion = fecha;
        this.actorModificacionId = actor;
    }
}

// Entidad de negocio hija
public final class Usuario extends EntidadAuditada {
    public void darDeBaja(LocalDateTime fechaModificacion, ActorAuditoriaId actor) {
        if (estaDadoDeBaja()) throw new IllegalStateException("El usuario ya se encuentra dado de baja");
        
        // 1. Auditar ANTES de mutar
        registrarModificacion(fechaModificacion, actor);
        
        // 2. Modificar el estado local
        this.estadoCuenta = EstadoCuenta.BAJA;
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Declarar `public` los metodos de mutacion de auditoria como `registrarModificacion`, exponiendolos al exterior del agregado de dominio.
- Crear una clase global `BaseEntity` en la capa de persistencia en lugar del dominio (la auditoria es regla del negocio, no un detalle de base de datos).

## Instrucciones Especificas para Agentes IA

- Siempre que construyas una nueva entidad de dominio principal que requiera trazabilidad, haz que herede de `EntidadAuditada`.
- Asegurate de que los constructores de inicializacion del dominio soliciten los campos obligatorios de la clase padre (`fechaCreacion`, `actorCreacionId`).
