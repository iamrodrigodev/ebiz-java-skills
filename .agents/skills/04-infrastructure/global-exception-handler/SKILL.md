---
name: global-exception-handler
description: Guía oficial de manejo de errores HTTP y el payload ApiErrorResponse.
---

# Guía de Implementación: Global Exception Handler

El manejo centralizado de excepciones traduce los errores de negocio puros (`IllegalStateException`, `RuntimeException` con causas de negocio) a respuestas HTTP correctas (`409 Conflict`, `404 Not Found`).

## 1. El Formato de Respuesta (`ApiErrorResponse`)

```java
package pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.adapter.in.rest.exception;

import java.time.LocalDateTime;

public record ApiErrorResponse(
    String codigo,
    String mensaje,
    int estadoHttp,
    LocalDateTime fecha
) {}
```

## 2. El Manejador de Spring (`GlobalExceptionHandler`)

```java
package pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.adapter.in.rest.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import pe.com.mcalderon.logistica.identidades.gestionusuarios.application.exception.CorreoElectronicoYaRegistradoException;
import pe.com.mcalderon.logistica.identidades.gestionusuarios.application.exception.UsuarioNoExisteException;
import java.time.LocalDateTime;

@RestControllerAdvice
public final class GlobalExceptionHandler {

    @ExceptionHandler(CorreoElectronicoYaRegistradoException.class)
    public ResponseEntity<ApiErrorResponse> manejarCorreoElectronicoYaRegistrado(
        CorreoElectronicoYaRegistradoException exception
    ) {
        HttpStatus estado = HttpStatus.CONFLICT;
        ApiErrorResponse respuesta = new ApiErrorResponse(
            "CORREO_ELECTRONICO_YA_REGISTRADO",
            exception.getMessage(),
            estado.value(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(estado).body(respuesta);
    }

    @ExceptionHandler(UsuarioNoExisteException.class)
    public ResponseEntity<ApiErrorResponse> manejarUsuarioNoExiste(
        UsuarioNoExisteException exception
    ) {
        ApiErrorResponse respuesta = new ApiErrorResponse(
            "USUARIO_NO_EXISTE",
            exception.getMessage(),
            HttpStatus.NOT_FOUND.value(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(respuesta);
    }
}
```

## Reglas de Implementación
- Todas las excepciones de la aplicación y del dominio se centralizan aquí.
- Retornar siempre un `ResponseEntity<ApiErrorResponse>`.
- Asignar a mano el código funcional de error (ej. `"CORREO_ELECTRONICO_YA_REGISTRADO"`).
