---
name: create-port
description: Guía y ejemplos de código sobre cómo generar Puertos de entrada y salida (In/Out).
---

# Guía de Implementación: Puertos (Interfaces)

## Reglas de Implementación

### 1. Puerto de Entrada (`application/port/in/ActivarUsuarioUseCase.java`)
Es un contrato puro de Java. Define lo que el sistema **ofrece** al exterior. Es llamado por los controladores (adapter.in) y ejecutado por los Casos de Uso.

```java
package com.empresa.logistica.gestionusuarios.application.port.in;

import com.empresa.logistica.gestionusuarios.application.command.ActivarUsuarioCommand;

public interface ActivarUsuarioUseCase {
    void execute(ActivarUsuarioCommand command);
}
```

### 2. Puerto de Salida (`application/port/out` o `infrastructure/adapter/out`)
**EXCEPCIÓN DEL EQUIPO (LEY JPA):** A diferencia de la Arquitectura Hexagonal estricta (donde los puertos out son interfaces puras), en este proyecto hemos acordado utilizar directamente **`JpaRepository`**. 

Por lo tanto, en lugar de crear una interfaz pura y luego un adaptador que la implemente, crearemos directamente la interfaz JPA en la capa correspondiente (usualmente tratada como el puerto de salida).

```java
package com.empresa.logistica.gestionusuarios.infrastructure.adapter.out.persistence;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

// REGLA DEL EQUIPO: Usamos directamente JpaRepository en lugar de interfaces puras de Java
@Repository
public interface UsuarioRepository extends JpaRepository<UsuarioJpaEntity, String> {
    boolean existsByCorreo(String correo);
}
```

## Restricciones Finales
Al programar puertos de salida:
- **No** crees interfaces de Java puro que luego envuelvas con adaptadores complejos, a menos que sea un cliente HTTP externo. Para base de datos, usa Spring Data JPA directamente.

