---
name: create-usecase
description: Guía y ejemplos de código sobre cómo generar la lógica de un Caso de Uso (Application Service).
---

# Guía de Implementación: Casos de Uso

Este documento establece el estándar del equipo (tanto para desarrolladores humanos como para agentes de IA) para generar Casos de Uso (Application Services). El objetivo principal es garantizar que esta capa cumpla con el aislamiento requerido por la Arquitectura Hexagonal, sin depender de frameworks externos.

## Reglas de Implementación

Al crear un Caso de Uso, se deben generar las siguientes piezas de código en la capa `application` del módulo correspondiente:

1. **El Command (DTO de entrada)** en `application/command`.
2. **La Implementación del Servicio** en `application/service`.

El servicio siempre debe implementar una interfaz `port.in` previamente definida y usar interfaces `port.out` para comunicarse con la base de datos o servicios externos.

## Ejemplos de Código (Patrón Oficial del Equipo)

### 1. El Command (`application/command/ActivarUsuarioCommand.java`)
Es un simple registro o DTO inmutable que transporta datos. Se recomienda mantenerlo libre de anotaciones de frameworks externos a menos que el equipo haya acordado el uso de `jakarta.validation`.

```java
package com.empresa.logistica.gestionusuarios.application.command;

public record ActivarUsuarioCommand(
    String usuarioId,
    String motivo
) {
    public ActivarUsuarioCommand {
        if (usuarioId == null || usuarioId.isBlank()) {
            throw new IllegalArgumentException("El ID de usuario es requerido");
        }
    }
}
```

### 2. La Implementación del Caso de Uso (`application/service/ActivarUsuarioService.java`)
Esta clase **NO** lleva `@Service` ni `@Autowired`. La inyección se hace por constructor y la configuración de beans ocurrirá en infrastructure. Es Java puro.

```java
package com.empresa.logistica.gestionusuarios.application.service;

import com.empresa.logistica.gestionusuarios.application.port.in.ActivarUsuarioUseCase;
import com.empresa.logistica.gestionusuarios.application.port.out.UsuarioRepositoryPort;
import com.empresa.logistica.gestionusuarios.application.command.ActivarUsuarioCommand;
import com.empresa.logistica.gestionusuarios.domain.model.Usuario;
import com.empresa.logistica.gestionusuarios.domain.exception.UsuarioNoEncontradoException;

// REGLA: PROHIBIDO USAR @Service de Spring aquí.
public class ActivarUsuarioService implements ActivarUsuarioUseCase {

    private final UsuarioRepositoryPort usuarioRepository;

    // Inyección pura por constructor
    public ActivarUsuarioService(UsuarioRepositoryPort usuarioRepository) {
        this.usuarioRepository = usuarioRepository;
    }

    @Override
    public void execute(ActivarUsuarioCommand command) {
        // 1. Obtener del repositorio (Port Out)
        Usuario usuario = usuarioRepository.findById(command.usuarioId())
            .orElseThrow(() -> new UsuarioNoEncontradoException(command.usuarioId()));
        
        // 2. Ejecutar comportamiento del dominio rico
        usuario.activar(command.motivo());
        
        // 3. Guardar cambios usando el puerto de salida
        usuarioRepository.save(usuario);
    }
}
```

## Restricciones Finales
Al revisar o generar este código:
- Asegúrate de que no exista ningún `import org.springframework.*` en estos archivos.
- Valida que la lógica de negocio pura (como verificar si el usuario puede activarse) esté encapsulada dentro de la Entidad (ej. `usuario.activar()`), y no expuesta al aire en este Servicio.

