---
name: rest-mappers
description: Guía oficial de mapeo manual en REST usando Objects.requireNonNull.
---

# Guía de Implementación: REST Mappers

Los mappers actúan como mecanismo de transformación técnica aislando el Dominio del protocolo HTTP y evitando exponer accidentalmente atributos (principio de mínima exposición). 

**DECISIÓN DE ARQUITECTURA:** Se realiza el mapeo MANUALMENTE, sin depender de librerías automáticas, para garantizar un control estricto y la inmutabilidad.

## Ejemplo de Implementación Exacta

```java
package pe.com.mcalderon.logistica.identidades.gestionusuario.infrastructure.adapter.in.rest.mapper;

import pe.com.mcalderon.logistica.identidades.gestionusuarios.application.command.CrearUsuarioCommand;
import pe.com.mcalderon.logistica.identidades.gestionusuarios.domain.model.UsuarioId;
import pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.adapter.in.rest.request.CrearUsuarioRequest;
import pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.adapter.in.rest.response.CrearUsuarioResponse;
import java.util.Objects;

public final class CrearUsuarioRestMapper {
    
    public CrearUsuarioCommand aCommand(CrearUsuarioRequest request) {
        Objects.requireNonNull(request, "CrearUsuarioRequest no puede ser nulo");
        
        return new CrearUsuarioCommand(
            request.personaId(),
            request.nombre(),
            request.correo(),
            request.origen(),
            request.tipoCuenta(),
            request.fechaInicioVigencia(),
            request.actorCreacionId()
        );
    }
    
    public CrearUsuarioResponse aResponse(UsuarioId usuarioId) {
        Objects.requireNonNull(usuarioId, "UsuarioId no puede ser nulo");
        return new CrearUsuarioResponse(usuarioId.valor());
    }
}
```

## Reglas Clave
- Usar `Objects.requireNonNull` en las entradas del mapper.
- La clase mapper debe ser `final`.
- El controlador simplemente delega la conversión a esta clase antes de llamar al UseCase.
