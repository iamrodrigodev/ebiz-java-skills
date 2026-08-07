---
name: create-usecase
description: Guía y ejemplos de código sobre cómo generar la lógica de un Caso de Uso (Application Service).
---

# Guía de Implementación: Casos de Uso

Este documento establece el estándar del equipo (tanto para desarrolladores humanos como para agentes de IA) para generar Casos de Uso (Application Services). El objetivo principal es garantizar que esta capa cumpla con el aislamiento requerido por la Arquitectura Hexagonal, **aprovechando las ventajas de validación de Spring/Jakarta**.

## Reglas de Implementación

Al crear un Caso de Uso, se deben generar las siguientes piezas de código en la capa `application` del módulo correspondiente:

1. **El Command (DTO de entrada)** en `application/command`.
2. **La Implementación del Servicio** en `application/service`.

## Ejemplos de Código (Patrón Oficial del Equipo)

### 1. El Command (`application/command/ActivarUsuarioCommand.java`)
Es un simple registro o DTO inmutable. El equipo **fomenta** el uso de Jakarta Validation (`@NotNull`, `@NotBlank`) para blindar el Caso de Uso, evitando código manual repetitivo.

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

### 2. La Implementación del Caso de Uso (`application/service/ActivarUsuarioService.java`)
Esta clase **NO** lleva `@Service` ni `@Autowired`. La inyección se hace por constructor y la configuración de beans ocurrirá en infraestructura. Es Java puro.

```java
package com.empresa.logistica.gestionusuarios.application.service;

import com.empresa.logistica.gestionusuarios.application.port.in.ActivarUsuarioUseCase;
import com.empresa.logistica.gestionusuarios.application.port.out.UsuarioRepository;
import com.empresa.logistica.gestionusuarios.application.command.ActivarUsuarioCommand;
import com.empresa.logistica.gestionusuarios.domain.model.Usuario;
import com.empresa.logistica.gestionusuarios.domain.exception.UsuarioNoEncontradoException;

// REGLA: PROHIBIDO USAR @Service de Spring aquí.
public class ActivarUsuarioService implements ActivarUsuarioUseCase {

    private final UsuarioRepository usuarioRepository;

    // Inyección pura por constructor
    public ActivarUsuarioService(UsuarioRepository usuarioRepository) {
        this.usuarioRepository = usuarioRepository;
    }

    @Override
    public void execute(ActivarUsuarioCommand command) {
        // En este punto, el 'command' ya fue validado automáticamente por los Controladores
        
        // 1. Obtener del repositorio (Port Out) - Ejemplo asumiendo inyección directa JPA
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
Al revisar o generar este código:
- Asegúrate de que no exista ningún `import org.springframework.stereotype.Service` en estos archivos.
- Valida que la lógica de negocio pura (como verificar si el usuario puede activarse) esté encapsulada dentro de la Entidad (ej. `usuario.activarCuenta()`), y no expuesta al aire en este Servicio.
