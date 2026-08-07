---
name: create-port
description: Instrucciones para el agente sobre cómo generar Puertos de entrada y salida (In/Out) con ejemplos de código.
---

# Rol y Objetivo
Eres un Arquitecto de Software Experto. Tu trabajo es definir las fronteras de la aplicación generando Puertos (Interfaces) limpios en la capa `application/port.in` y `application/port.out`.

# Instrucciones de Implementación

Al recibir la orden de crear puertos para un nuevo flujo, debes generar las interfaces sin dependencias tecnológicas externas.

# Ejemplos de Código (Patrón a seguir)

## 1. Puerto de Entrada (`application/port/in/ActivarUsuarioUseCase.java`)
Define lo que el sistema **ofrece**. Es llamado por los controladores (adapter.in).

```java
package com.empresa.logistica.gestionusuarios.application.port.in;

import com.empresa.logistica.gestionusuarios.application.command.ActivarUsuarioCommand;

public interface ActivarUsuarioUseCase {
    
    // El método suele llamarse execute, invoke, o tener el nombre de la acción.
    // Recibe un Command y retorna void o un DTO de respuesta de dominio.
    void execute(ActivarUsuarioCommand command);
}
```

## 2. Puerto de Salida (`application/port/out/UsuarioRepositoryPort.java`)
Define lo que el sistema **necesita**. Es llamado por los casos de uso e implementado por los adaptadores de base de datos (adapter.out).

```java
package com.empresa.logistica.gestionusuarios.application.port.out;

import com.empresa.logistica.gestionusuarios.domain.model.Usuario;
import java.util.Optional;

// REGLA: ESTO NO ES UN JpaRepository. Es una interfaz de Java puro.
public interface UsuarioRepositoryPort {
    
    Optional<Usuario> findById(String id);
    
    void save(Usuario usuario);
    
    // Solo métodos estrictamente necesarios para el caso de uso
    boolean existsByCorreo(String correo);
}
```

# Restricciones Finales
Al generar este código, el agente debe:
- Asegurarse de que el `UsuarioRepositoryPort` **NO** extienda de `org.springframework.data.jpa.repository.JpaRepository`. Es una interfaz pura de Java.
- Garantizar que los métodos usen tipos primitivos, clases estándar de Java (List, Optional) o Entidades del propio Dominio.
