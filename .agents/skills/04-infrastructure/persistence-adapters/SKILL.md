---
name: persistence-adapters
description: Instrucciones para el agente sobre cómo generar Adaptadores (REST/Persistencia) en infrastructure con ejemplos de código.
---

# Rol y Objetivo
Eres un Desarrollador Experto en Spring Boot. Tu objetivo es conectar los Puertos limpios de la Aplicación con el mundo real (Bases de Datos, APIs REST), creando código en la capa `infrastructure`. Aquí SÍ está permitido y es obligatorio el uso de frameworks.

# Instrucciones de Implementación

Al recibir la orden de crear adaptadores, generarás el Controlador REST (`adapter.in.rest`) y/o el Repositorio JPA (`adapter.out.persistence`).

# Ejemplos de Código (Patrón a seguir)

## 1. Adaptador de Entrada (Controlador REST)
Ubicación: `infrastructure/adapter/in/rest/UsuarioController.java`

```java
package com.empresa.logistica.gestionusuarios.infrastructure.adapter.in.rest;

import com.empresa.logistica.gestionusuarios.application.port.in.ActivarUsuarioUseCase;
import com.empresa.logistica.gestionusuarios.application.command.ActivarUsuarioCommand;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/v1/usuarios")
public class UsuarioController {

    private final ActivarUsuarioUseCase activarUsuarioUseCase;

    // Se inyecta la interfaz del caso de uso (Puerto In)
    public UsuarioController(ActivarUsuarioUseCase activarUsuarioUseCase) {
        this.activarUsuarioUseCase = activarUsuarioUseCase;
    }

    @PostMapping("/{id}/activar")
    public ResponseEntity<Void> activarUsuario(
            @PathVariable("id") String id, 
            @RequestBody ActivarUsuarioRequest request) { // Request web específico
        
        // Mapear request web al Command de la aplicación
        ActivarUsuarioCommand command = new ActivarUsuarioCommand(id, request.motivo());
        
        // Ejecutar caso de uso
        activarUsuarioUseCase.execute(command);
        
        return ResponseEntity.ok().build();
    }
}
```

## 2. Adaptador de Salida (Persistencia JPA)
Ubicación: `infrastructure/adapter/out/persistence/UsuarioPersistenceAdapter.java`

Este adaptador implementa el `Port Out` y usa un repositorio interno de Spring Data JPA.

```java
package com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence;

import com.empresa.logistica.gestionusuarios.application.port.out.UsuarioRepositoryPort;
import com.empresa.logistica.gestionusuarios.domain.model.Usuario;
import org.springframework.stereotype.Repository;
import java.util.Optional;

@Repository
public class UsuarioPersistenceAdapter implements UsuarioRepositoryPort {

    private final SpringDataUsuarioRepository jpaRepository; // La interfaz que extiende JpaRepository
    private final UsuarioEntityMapper mapper; // Clase para mapear de Dominio a JPA y viceversa

    public UsuarioPersistenceAdapter(SpringDataUsuarioRepository jpaRepository, UsuarioEntityMapper mapper) {
        this.jpaRepository = jpaRepository;
        this.mapper = mapper;
    }

    @Override
    public Optional<Usuario> findById(String id) {
        return jpaRepository.findById(id)
                .map(mapper::toDomain); // Convertir UsuarioJpaEntity a Usuario (Domain)
    }

    @Override
    public void save(Usuario usuario) {
        UsuarioJpaEntity entity = mapper.toJpaEntity(usuario);
        jpaRepository.save(entity);
    }
    
    @Override
    public boolean existsByCorreo(String correo) {
        return jpaRepository.existsByCorreo(correo);
    }
}
```

# Restricciones Finales
Al generar este código, el agente debe:
- No devolver Entidades de dominio directamente en los controladores REST. Si hay que devolver datos, se debe mapear la Entidad a un DTO de respuesta (ej. `UsuarioResponse`).
- Mantener la separación estricta: La base de datos guarda `UsuarioJpaEntity` (con anotaciones `@Entity`, `@Table`), pero el adaptador lo mapea y le entrega al caso de uso un objeto `Usuario` (modelo puro de dominio).

