---
name: transactions
description: Guía oficial del equipo para el manejo de transacciones de base de datos (@Transactional).
---

# Guía de Implementación: Transacciones (@Transactional)

El manejo de transacciones garantiza la integridad de los datos (ACID) frente a fallos. Esta guía dicta cómo y dónde los desarrolladores y agentes deben aplicar transaccionalidad.

## Reglas de Transaccionalidad

### 1. Ubicación de @Transactional
La anotación `@Transactional` de Spring **DEBE** colocarse en la capa de `application/service` (en los Casos de Uso). 
Aunque el caso de uso es agnóstico a la infrastructure, el control de la transacción del negocio es responsabilidad del caso de uso.

### 2. Ejemplo de Implementación (Patrón Oficial)

```java
package com.empresa.logistica.gestionusuarios.application.service;

import org.springframework.transaction.annotation.Transactional;
import com.empresa.logistica.gestionusuarios.application.port.in.ActivarUsuarioUseCase;
import com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence.repository.UsuarioRepository;
// ... imports

public class ActivarUsuarioService implements ActivarUsuarioUseCase {

    private final UsuarioRepository usuarioRepository;

    public ActivarUsuarioService(UsuarioRepository usuarioRepository) {
        this.usuarioRepository = usuarioRepository;
    }

    // REGLA: Transacción de escritura por defecto
    @Override
    @Transactional
    public void execute(ActivarUsuarioCommand command) {
        // Todo este bloque se ejecutará dentro de una única transacción de BD
        UsuarioJpaEntity entity = usuarioRepository.findById(command.usuarioId())
            .orElseThrow(() -> new RuntimeException("Usuario no encontrado"));
            
        entity.setEstado("ACTIVO");
        usuarioRepository.save(entity);
    }
}
```

### 3. Consultas de Solo Lectura (Read-Only)
Para casos de uso que únicamente consultan datos (sin modificar, insertar ni borrar), se debe usar `readOnly = true`. Esto optimiza enormemente el rendimiento en Hibernate y la base de datos al evitar el *dirty checking*.

```java
    @Transactional(readOnly = true)
    public UsuarioDTO buscarUsuario(String id) {
        return usuarioRepository.findById(id)
                .map(mapper::toDto)
                .orElseThrow(() -> new RuntimeException("No encontrado"));
    }
```

## Instrucciones para el Agente (LLM)
- Revisa siempre si el Caso de Uso hace modificaciones (INSERT/UPDATE/DELETE). Si es así, pon `@Transactional`.
- Si el Caso de Uso es puramente de consulta (SELECT), pon `@Transactional(readOnly = true)` sin excepciones.
- Nunca pongas `@Transactional` en los Controladores (`@RestController`), ya que la transacción se mantendría abierta durante la red, saturando el pool de conexiones a la base de datos.

