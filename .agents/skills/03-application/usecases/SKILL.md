---
name: usecases
description: GuÃ­a y ejemplos de cÃ³digo sobre cÃ³mo generar la lÃ³gica de un Caso de Uso (Application Service).
---

# GuÃ­a de ImplementaciÃ³n: Casos de Uso

Este documento establece el estÃ¡ndar del equipo (tanto para desarrolladores humanos como para agentes de IA) para generar Casos de Uso (Application Services). El objetivo principal es garantizar que esta capa cumpla con el aislamiento requerido por la Arquitectura Hexagonal, **aprovechando las ventajas de validaciÃ³n de Spring/Jakarta**.

## Reglas de ImplementaciÃ³n

Al crear un Caso de Uso, se deben generar las siguientes piezas de cÃ³digo en la capa `application` del mÃ³dulo correspondiente:

1. **El Command (DTO de entrada)** en `application/command`.
2. **La ImplementaciÃ³n del Servicio** en `application/service`.

## Ejemplos de CÃ³digo (PatrÃ³n Oficial del Equipo)

### 1. El Command (`application/command/ActivarUsuarioCommand.java`)
Es un simple registro o DTO inmutable. El equipo **fomenta** el uso de Jakarta Validation (`@NotNull`, `@NotBlank`) para blindar el Caso de Uso, evitando cÃ³digo manual repetitivo.

```java
package com.empresa.logistica.gestionusuarios.application.command;

import jakarta.validation.constraints.NotBlank;
import lombok.Builder;

// Se permite usar Record de Java o Clases con @Value/@Builder de Lombok
@Builder
public record ActivarUsuarioCommand(
    @NotBlank(message = "El ID de usuario es requerido")
    String usuarioId,
    
    @NotBlank(message = "Debe proporcionar un motivo")
    String motivo
) {
}
```

### 2. La ImplementaciÃ³n del Caso de Uso (`application/service/ActivarUsuarioService.java`)
Esta clase **NO** lleva `@Service` ni `@Autowired`. La inyecciÃ³n se hace por constructor y la configuraciÃ³n de beans ocurrirÃ¡ en infraestructura. Es Java puro.

```java
package com.empresa.logistica.gestionusuarios.application.service;

import com.empresa.logistica.gestionusuarios.application.port.in.ActivarUsuarioUseCase;
import com.empresa.logistica.gestionusuarios.application.port.out.UsuarioRepository;
import com.empresa.logistica.gestionusuarios.application.command.ActivarUsuarioCommand;
import com.empresa.logistica.gestionusuarios.domain.model.Usuario;
import com.empresa.logistica.gestionusuarios.domain.exception.UsuarioNoEncontradoException;

// REGLA: PROHIBIDO USAR @Service de Spring aquÃ­.
public class ActivarUsuarioService implements ActivarUsuarioUseCase {

    private final UsuarioRepository usuarioRepository;

    // InyecciÃ³n pura por constructor
    public ActivarUsuarioService(UsuarioRepository usuarioRepository) {
        this.usuarioRepository = usuarioRepository;
    }

    @Override
    public void execute(ActivarUsuarioCommand command) {
        // En este punto, el 'command' ya fue validado automÃ¡ticamente por los Controladores
        
        // 1. Obtener del repositorio (Port Out) - Ejemplo asumiendo inyecciÃ³n directa JPA
        UsuarioJpaEntity entity = usuarioRepository.findById(command.usuarioId())
            .orElseThrow(() -> new UsuarioNoEncontradoException(command.usuarioId()));
        
        Usuario usuario = UsuarioMapper.toDomain(entity);
        
        // 2. Ejecutar comportamiento del dominio rico
        usuario.activarCuenta(command.motivo());
        
        // 3. Guardar cambios usando el puerto de salida
        usuarioRepository.save(UsuarioMapper.toJpaEntity(usuario));
    }
}
```

## Restricciones Finales
Al revisar o generar este cÃ³digo:
- AsegÃºrate de que no exista ningÃºn `import org.springframework.stereotype.Service` en estos archivos.
- Valida que la lÃ³gica de negocio pura (como verificar si el usuario puede activarse) estÃ© encapsulada dentro de la Entidad (ej. `usuario.activarCuenta()`), y no expuesta al aire en este Servicio.
