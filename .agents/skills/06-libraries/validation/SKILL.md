---
name: validation
description: Estandar de validacion sintactica con Jakarta Validation.
---

# Guia de Validaciones (Jakarta Validation)

**Objetivo:** Separar las responsabilidades de validacion. El formateo sintactico (`@NotNull`, `@Email`) se atiende superficialmente en la frontera de entrada (HTTP) para devolver errores 400 Bad Request rapidos y automaticos, protegiendo al resto de la aplicacion de trafico basura.

## Reglas de Implementacion y Arquitectura

1. **Solo en la Frontera Externa (Infrastructure):** Las anotaciones de `jakarta.validation.constraints.*` solo estan permitidas en los `Request` DTOs recibidos por los Controladores.
2. **Dominio Libre de Librerias:** NUNCA debes poner `@NotNull` ni ninguna anotacion de la API de validacion dentro de la capa `domain/model`. El Dominio valida a traves de su codigo puro nativo y Excepciones.

## Lo que SI debes hacer (Buenas Practicas)

```java
package pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.adapter.in.rest.request;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Positive;
import java.time.LocalDate;

// Validacion declarativa permitida SOLO en el Request (Capa de Infraestructura)
public record CrearUsuarioRequest(
    
    @Positive(message = "El identificador de la persona debe ser mayor que cero")
    Long personaId,
    
    @NotBlank(message = "El correo electronico no puede estar vacio")
    @Email(message = "El correo electronico no tiene un formato valido")
    String correo,
    
    @NotNull(message = "La fecha inicial de vigencia es obligatoria")
    LocalDate fechaInicioVigencia
) {}
```

## Lo que NO debes hacer (Anti-patrones)

- Poner anotaciones en el `Command` (Application). El Command solo es instanciado por el Mapper, no llega directamente de la web, asi que la anotacion ahi no tiene ningun gatillo de validacion automatico util.

```java
// ERROR CATASTROFICO: Librerias externas en el modelo puro
package pe.com.mcalderon.logistica.identidades.gestionusuarios.domain.model;

import jakarta.validation.constraints.NotNull;

public class Usuario {
    @NotNull // PROHIBIDO en el dominio
    private String nombre;
}
```

## Instrucciones Especificas para Agentes IA

- Siempre que crees un `<Operacion>Request` anadele la dependencia importada de `jakarta.validation.constraints.*` acompanada de sus parametros customizables de `message`.
- Nunca anadas estas dependencias en el Codigo de `domain/model`, ni `application/command`. El negocio prefiere el viejo y confiable `if (algo == null) throw new ...`.
