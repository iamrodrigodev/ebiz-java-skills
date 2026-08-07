---
name: rest-api
description: Guia oficial para la construccion de Controladores REST (Input Adapters).
---

# Guia de Controladores REST (Input Adapters)

**Objetivo:** Implementar adaptadores de entrada (Controladores) que traduzcan el protocolo web (HTTP) hacia las intenciones puras del caso de uso. El controlador es un adaptador tonto que solo escucha, enruta y formatea la salida HTTP.

## Reglas de Implementacion y Arquitectura

1. **Responsabilidad Estricta:** El controlador NO aplica reglas de negocio, no calcula vigencias, no captura reglas de base de datos ni utiliza inyeccion por `@Autowired`.
2. **Dependencias Correctas:** El controlador SOLO conoce a los Puertos de Entrada (`*UseCase`) y al Mapper (`*RestMapper`). NUNCA depende del Application Service directamente.
3. **HTTP Semantico:** Las respuestas deben usar codigos estandares (201 Created para creaciones, 204 No Content para eliminaciones/actualizaciones, 200 OK para lecturas).

## Lo que SI debes hacer (Buenas Practicas)

```java
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.Objects;

@RestController
@RequestMapping("/api/usuarios")
public final class UsuarioController {

    private final CrearUsuarioUseCase crearUsuarioUseCase;
    private final CrearUsuarioRestMapper crearUsuarioRestMapper;

    public UsuarioController(
        CrearUsuarioUseCase crearUsuarioUseCase,
        CrearUsuarioRestMapper crearUsuarioRestMapper
    ) {
        this.crearUsuarioUseCase = Objects.requireNonNull(crearUsuarioUseCase, "UseCase nulo");
        this.crearUsuarioRestMapper = Objects.requireNonNull(crearUsuarioRestMapper, "Mapper nulo");
    }

    @PostMapping
    public ResponseEntity<CrearUsuarioResponse> crear(
        @Valid @RequestBody CrearUsuarioRequest request
    ) {
        // 1. Traducir de JSON a Command
        CrearUsuarioCommand command = crearUsuarioRestMapper.aCommand(request);
        
        // 2. Invocar el Caso de Uso (Retorna el Value Object del ID generado)
        UsuarioId usuarioId = crearUsuarioUseCase.crearUsuario(command);
        
        // 3. Traducir el resultado al contrato de Salida
        CrearUsuarioResponse response = crearUsuarioRestMapper.aResponse(usuarioId);
        
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Construir validaciones funcionales dentro del metodo `@PostMapping`.
- Utilizar Entidades del Dominio o Value Objects como parametros de firma del metodo HTTP (`public ResponseEntity crear(@RequestBody CorreoElectronico correo)`). Siempre usa Request/Response DTOs.

## Instrucciones Especificas para Agentes IA

- Si el IDE o las validaciones marcan error sobre _"Could not autowire. No beans of 'XUseCase' found"_, **IGNORALO**. En esta arquitectura es intencional no poner anotaciones `@Service` en el dominio, el ensamblaje de Beans ocurre al final en la clase `Configuration` central. No intentes "reparar" la inyeccion de Spring alterando las clases puras.
