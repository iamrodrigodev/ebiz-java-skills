---
name: value-objects
description: Guía oficial para la creación de Value Objects y Enums del Dominio (Extraído del PDF Oficial).
---

# Guía de Implementación: Value Objects

En la Arquitectura Hexagonal y DDD, no todo debe ser una Entidad. Los **Value Objects** (Objetos de Valor) son cruciales para el tipado fuerte y la validación intrínseca en su punto de verdad local.

## 1. Value Objects de Tipado Fuerte

Evitamos usar tipos primitivos genéricos (`String`, `Long`). Creamos records o clases finales.

### Ejemplo: `CorreoElectronico` (Con Validación Regex)
```java
package pe.com.mcalderon.logistica.identidades.gestionusuarios.domain.model;

import java.util.Locale;
import java.util.Objects;
import java.util.regex.Pattern;

public final class CorreoElectronico {
    private static final Pattern FORMATO_VALIDO = 
        Pattern.compile("^[^\\s@]+@[^\\s@]+\\.[^\\s@]+$");
    
    private final String valor;

    public CorreoElectronico(String valor) {
        if (valor == null) {
            throw new IllegalArgumentException("El correo electrónico no puede ser nulo");
        }
        String valorNormalizado = valor.trim().toLowerCase(Locale.ROOT);
        if (valorNormalizado.isBlank()) {
            throw new IllegalArgumentException("El correo electrónico no puede estar vacío");
        }
        if (!FORMATO_VALIDO.matcher(valorNormalizado).matches()) {
            throw new IllegalArgumentException("El correo electrónico no tiene un formato válido");
        }
        this.valor = valorNormalizado;
    }

    public String valor() { return valor; }
    // Implementar equals y hashCode
}
```

### Ejemplo: `PeriodoVigencia` (Con validación compuesta)
```java
public final class PeriodoVigencia {
    private final LocalDate fechaInicio;
    private final LocalDate fechaFin;

    public PeriodoVigencia(LocalDate fechaInicio, LocalDate fechaFin) {
        if (fechaInicio == null) throw new IllegalArgumentException("La fecha de inicio de vigencia no puede ser nula");
        if (fechaFin == null) throw new IllegalArgumentException("La fecha de fin de vigencia no puede ser nula");
        if (fechaFin.isBefore(fechaInicio)) {
            throw new IllegalArgumentException("La fecha de fin de vigencia no puede ser anterior a la fecha de inicio");
        }
        this.fechaInicio = fechaInicio;
        this.fechaFin = fechaFin;
    }

    public boolean estaVigente(LocalDate fechaEvaluacion) {
        if (fechaEvaluacion == null) throw new IllegalArgumentException("La fecha de evaluación no puede ser nula");
        return !fechaEvaluacion.isBefore(fechaInicio) && !fechaEvaluacion.isAfter(fechaFin);
    }
}
```

## 2. Enumeraciones del Dominio (Enums)

Al tener un universo cerrado de estados u orígenes, se usa Enum. **Decisión de Arquitectura:** Las reglas se aplican como configuración de la aplicación sin requerir acceso a BD.

### Ejemplo: `OrigenUsuario`
```java
public enum OrigenUsuario {
    INTERNO('I'), EXTERNO('E');

    private final char codigo;
    OrigenUsuario(char codigo) { this.codigo = codigo; }
    public char codigo() { return codigo; }

    public static OrigenUsuario desdeCodigo(char codigo) {
        char codigoNormalizado = Character.toUpperCase(codigo);
        for (OrigenUsuario origen : values()) {
            if (origen.codigo == codigoNormalizado) return origen;
        }
        throw new IllegalArgumentException("Código de origen de usuario no válido: " + codigo);
    }
}
```
*(Se aplica el mismo patrón para `TipoCuenta` y `EstadoCuenta`)*.
