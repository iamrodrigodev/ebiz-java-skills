---
name: global-exception-handler
description: Estandar para el manejo centralizado de excepciones y devolucion de errores HTTP.
---

# Guia de Manejo de Errores (Global Exception Handler)

**Objetivo:** Prevenir el uso excesivo de bloques `try/catch` en los controladores, delegando la captura de excepciones a un manejador centralizado que traduce los problemas internos a respuestas HTTP seguras y uniformes.

## Reglas de Implementacion y Arquitectura

1. El formato de error hacia el exterior debe ocultar trazas de servidor y devolver unicamente informacion comprensible para el cliente, estructurado en un `ApiErrorResponse`.
2. Las excepciones de validacion de reglas de negocio (`IllegalStateException`, `IllegalArgumentException` o propias de Application) se traducen a codigos HTTP 409 Conflict, 404 Not Found o 400 Bad Request.

## Lo que SI debes hacer (Buenas Practicas)

```java
// 1. El payload inmutable de respuesta
public record ApiErrorResponse(
    String codigo,
    String mensaje,
    int estadoHttp,
    LocalDateTime fecha
) {}

// 2. El manejador central
@RestControllerAdvice
public final class GlobalExceptionHandler {

    @ExceptionHandler(CorreoElectronicoYaRegistradoException.class)
    public ResponseEntity<ApiErrorResponse> manejarCorreoElectronicoYaRegistrado(
        CorreoElectronicoYaRegistradoException exception
    ) {
        HttpStatus estado = HttpStatus.CONFLICT;
        ApiErrorResponse respuesta = new ApiErrorResponse(
            "CORREO_ELECTRONICO_YA_REGISTRADO", // Codigo leible por frontends
            exception.getMessage(),
            estado.value(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(estado).body(respuesta);
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Devolver excepciones nativas del entorno (como `DataIntegrityViolationException` de SQL) directamente al cliente HTTP. Esto genera vulnerabilidades de seguridad al exponer la tecnologia de base de datos.
- Usar bloques `try/catch` manuales dentro de los `@RestController` para armar respuestas `ResponseEntity.badRequest()`.

## Instrucciones Especificas para Agentes IA

- Siempre que la aplicacion arroje una excepcion nueva (ej. `PersonaNoExisteException`), debes integrarla a la clase `GlobalExceptionHandler` utilizando `@ExceptionHandler` con el `HttpStatus` mas adecuado.
- Nunca modifiques el record `ApiErrorResponse` propuesto, este formato es un contrato estricto de la API.
