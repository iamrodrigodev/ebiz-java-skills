---
name: value-objects
description: Guia de implementacion de Value Objects y Enums del Dominio.
---

# Guia de Objetos de Valor (Value Objects)

**Objetivo:** Erradicar la "obsesion por los primitivos" en el dominio implementando objetos inmutables que encapsulen la semantica del negocio, protegiendo la validez intrinseca de la data (el "punto de verdad local").

## Reglas de Implementacion y Arquitectura

1. **Tipado Fuerte:** Evitar variables genericas (`String correo`, `Long id`). En su lugar, se utilizan Value Objects (`CorreoElectronico`, `UsuarioId`).
2. **Verdad Local:** El Value Object es el unico lugar donde se valida el formato intrinseco (ej. Expresion Regular para el correo).
3. **Inmutabilidad Estricta:** Un Value Object no tiene ciclo de vida. Si su contenido varia, se crea un objeto nuevo.
4. **Dominio Cerrado (Enums):** Para catálogos o clasificaciones estáticas con un universo cerrado de valores, se utilizan `enum` garantizando las conversiones desde codigo de sistema (ej. Base de Datos o DTO).

## Lo que SI debes hacer (Buenas Practicas)

```java
// Implementacion estricta y profesional de un Value Object
public final class CorreoElectronico {
    private static final Pattern FORMATO_VALIDO = 
        Pattern.compile("^[^\\s@]+@[^\\s@]+\\.[^\\s@]+$");
    
    private final String valor;

    public CorreoElectronico(String valor) {
        if (valor == null) {
            throw new IllegalArgumentException("El correo electronico no puede ser nulo");
        }
        String valorNormalizado = valor.trim().toLowerCase(Locale.ROOT);
        if (valorNormalizado.isBlank()) {
            throw new IllegalArgumentException("El correo electronico no puede estar vacio");
        }
        if (!FORMATO_VALIDO.matcher(valorNormalizado).matches()) {
            throw new IllegalArgumentException("El correo electronico no tiene un formato valido");
        }
        this.valor = valorNormalizado;
    }

    public String valor() { return valor; }
    
    @Override
    public boolean equals(Object objeto) {
        if (this == objeto) return true;
        if (!(objeto instanceof CorreoElectronico correoElectronico)) return false;
        return Objects.equals(valor, correoElectronico.valor);
    }
    
    @Override
    public int hashCode() { return Objects.hash(valor); }
}
```

```java
// Implementacion de Enums con conversiones seguras
public enum TipoCuenta {
    PERSONAL('P'), SERVICIO('S');

    private final char codigo;
    TipoCuenta(char codigo) { this.codigo = codigo; }
    public char codigo() { return codigo; }

    public static TipoCuenta desdeCodigo(char codigo) {
        char codigoNormalizado = Character.toUpperCase(codigo);
        for (TipoCuenta tipo : values()) {
            if (tipo.codigo == codigoNormalizado) return tipo;
        }
        throw new IllegalArgumentException("Codigo de tipo de cuenta no valido: " + codigo);
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Utilizar el Value Object para ejecutar validaciones contra base de datos (ej. `correoElectronico.existeEnBd()`). El Value Object ignora por completo su entorno.
- Validar expresiones regulares de dominio en los Controladores o en los Casos de Uso. El conocimiento pertenece al objeto local.

## Instrucciones Especificas para Agentes IA

- Siempre que se identifiquen identificadores (`id`) se debe crear su Value Object representativo (`UsuarioId`, `PersonaId`).
- Todos los Value Objects deben sobrescribir los metodos `equals` y `hashCode` para asegurar la igualdad por valor, asi como el metodo `toString()`.
