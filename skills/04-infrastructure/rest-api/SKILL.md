---
name: rest-api
description: Guía oficial para la construcción de Controladores REST como adaptadores de entrada (Extraído del PDF Oficial).
---

# Guía de Implementación: Controladores REST

El controlador REST actúa como un **Adaptador de Entrada**. Recibe la solicitud HTTP, la transforma en una intención de aplicación, invoca el puerto de entrada (`UseCase`) y construye la respuesta HTTP.

## 1. Responsabilidades Estrictas
**El controlador NO DEBE:**
- Aplicar reglas de negocio.
- Calcular la vigencia o estado inicial.
- Consultar directamente a la base de datos o usar repositorios.
- Depender de la implementación concreta (`CrearUsuarioService`); debe depender de la interfaz (`CrearUsuarioUseCase`).

**El controlador SÍ DEBE:**
- Exponer el endpoint HTTP (ej. `@PostMapping`).
- Recibir el JSON en un Record (ej. `CrearUsuarioRequest`).
- Activar validaciones declarativas mediante `@Valid`.
- Utilizar Mappers (ej. `CrearUsuarioRestMapper`) para convertir a Commands.
- Devolver un HTTP Status semántico (201, 204, 200).

## 2. Ejemplo de Implementación (UsuarioController)

```java
package pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.adapter.in.rest.controller;

import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
// imports omitidos para brevedad

@RestController
@RequestMapping("/api/usuarios")
public final class UsuarioController {

    // Depender SIEMPRE de los puertos (UseCase) y NO de los servicios
    private final CrearUsuarioUseCase crearUsuarioUseCase;
    private final CrearUsuarioRestMapper crearUsuarioRestMapper;

    public UsuarioController(
        CrearUsuarioUseCase crearUsuarioUseCase,
        CrearUsuarioRestMapper crearUsuarioRestMapper
    ) {
        this.crearUsuarioUseCase = Objects.requireNonNull(crearUsuarioUseCase, "CrearUsuarioUseCase no puede ser nulo");
        this.crearUsuarioRestMapper = Objects.requireNonNull(crearUsuarioRestMapper, "Mapper no puede ser nulo");
    }

    @PostMapping
    public ResponseEntity<CrearUsuarioResponse> crear(
        @Valid @RequestBody CrearUsuarioRequest request
    ) {
        // 1. Traducir (Mapper)
        CrearUsuarioCommand command = crearUsuarioRestMapper.aCommand(request);
        
        // 2. Invocar (UseCase)
        UsuarioId usuarioId = crearUsuarioUseCase.crearUsuario(command);
        
        // 3. Responder (Mapper a Response)
        CrearUsuarioResponse response = crearUsuarioRestMapper.aResponse(usuarioId);
        
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
}
```

> [!WARNING] 
> Durante la creación del controlador, IntelliJ puede mostrar "Could not autowire. No beans of 'CrearUsuarioUseCase' found". **Esto es normal** en esta arquitectura porque la configuración de beans en Spring se ensamblará al final en la carpeta `config`. ¡No modifiques el controlador ni rompas el aislamiento por esta alerta!
