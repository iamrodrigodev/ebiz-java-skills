---
name: domain-model
description: Guía de diseño de Entidades de Dominio Rico (Rich Domain Models) y manual paso a paso para programadores e IA.
---

# Guía de Diseño: Entidades de Dominio Rico

Esta guía explica a los desarrolladores y dicta a los agentes de IA cómo construir Entidades de Dominio Rico aplicando los principios de Domain-Driven Design (DDD).

**REGLA DE TRABAJO EN PAREJA:** Cuando un humano y un agente diseñen juntos una Entidad, el agente NO debe generar código inmediatamente. Primero debe existir una "fase de diseño" donde el agente le hará preguntas al usuario para comprender el ciclo de vida y las reglas de negocio.

## Fase 1: Entrevista de Diseño (Checklist)
Antes de escribir código, se deben responder estas preguntas:
1. ¿Qué representa la entidad y cuál es su identificador único natural?
2. ¿Cuál es su ciclo de vida? (Definir todos los estados posibles, ej. PENDIENTE, ACTIVO).
3. ¿Qué comportamientos o acciones modifican la entidad? (Aplica **Lenguaje Ubicuo**: usa verbos exactos del negocio como `activarCuenta()`, `darDeBaja()`, evitando términos técnicos genéricos CRUD como `update()` o `modify()`).
4. ¿Qué validaciones o reglas de negocio internas protegen a estas acciones?

## Fase 2: Implementación (El Patrón Oficial)

Una vez claro el diseño, la Entidad se crea en `domain/model`. **Debe ser un Rich Domain Model**, capaz de proteger sus propios datos.
*Excepción del equipo:* Aceptamos el uso de Lombok (`@Getter`, `@Builder`) y Jakarta Validation (`@NotNull`) para reducir el código repetitivo, pero NO usamos `@Data` ni `@Setter`.

Ejemplo oficial del equipo:

```java
package com.empresa.logistica.gestionusuarios.domain.model;

import lombok.Getter;
import lombok.Builder;
import lombok.AccessLevel;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import java.util.UUID;
import java.time.LocalDateTime;

// REGLA: Cero anotaciones de persistencia (@Entity, @Table PROHIBIDAS).
// SÍ están permitidas las ayudas de Lombok y Validation.
@Getter
@Builder(access = AccessLevel.PACKAGE)
public class Usuario {

    @NotBlank
    private final String id;
    
    @NotBlank
    private String correo;
    
    @NotNull
    private EstadoUsuario estado;
    
    @NotNull
    private LocalDateTime fechaCreacion;

    public enum EstadoUsuario {
        PENDIENTE_ACTIVACION, ACTIVO, SUSPENDIDO, DADO_DE_BAJA
    }

    // Factory Method (Fábrica de Creación Segura)
    public static Usuario registrarNuevo(String correo) {
        if (correo == null || correo.isBlank()) {
            throw new IllegalArgumentException("El correo es obligatorio");
        }
        return Usuario.builder()
                .id(UUID.randomUUID().toString())
                .correo(correo)
                .estado(EstadoUsuario.PENDIENTE_ACTIVACION)
                .fechaCreacion(LocalDateTime.now())
                .build();
    }

    // ----------------------------------------------------------------------
    // COMPORTAMIENTO DEL NEGOCIO (RICH DOMAIN Y LENGUAJE UBICUO)
    // ----------------------------------------------------------------------

    public void activarCuenta(String motivo) {
        if (this.estado == EstadoUsuario.DADO_DE_BAJA) {
            throw new IllegalStateException("Un usuario dado de baja no puede reactivarse");
        }
        this.estado = EstadoUsuario.ACTIVO;
    }

    public void darDeBaja() {
        this.estado = EstadoUsuario.DADO_DE_BAJA;
    }

    public void cambiarCorreo(String nuevoCorreo) {
        if (this.estado == EstadoUsuario.SUSPENDIDO) {
            throw new IllegalStateException("Un usuario suspendido no puede cambiar su correo");
        }
        this.correo = nuevoCorreo;
    }
}
```

## Restricciones Generales
- Las violaciones de reglas de negocio se manejan arrojando `IllegalStateException` o excepciones personalizadas de `domain/exception`.
- Jamás se utiliza `@Entity` o de hereda de `Serializable` para mapear bases de datos.
- **Lenguaje Ubicuo**: Todo método público debe nombrarse con verbos funcionales (`activarCuenta()`), estando totalmente prohibidos los setters clásicos (`setEstado()`) para exponer cambios críticos.
