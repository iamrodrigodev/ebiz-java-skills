---
name: validation
description: Guía oficial del equipo sobre el uso de Jakarta Validation (@Valid, @NotNull).
---

# Guía de Implementación: Jakarta Validation

No debemos reinventar la rueda validando campos nulos a mano. El equipo adopta el estándar **Jakarta Validation API** (`jakarta.validation.constraints.*`) para garantizar la integridad de los datos de forma declarativa.

## 1. Validación en la Entrada (Controladores)
- **Uso Obligatorio:** Todo request DTO (`adapter/in/rest/dto`) que entre por un Controlador debe validarse con `@Valid` o `@Validated`.
- **Anotaciones:** Úsalas libremente (`@NotNull`, `@NotBlank`, `@Size`, `@Email`).

## 2. Validación en la Aplicación (Commands)
- Es una excelente práctica blindar los Casos de Uso. 
- Los **Commands** (DTOs de entrada a la capa de aplicación) deben tener validaciones de Jakarta para asegurar que el Caso de Uso jamás reciba un parámetro inválido.

## 3. Validación en el Dominio (Excepción del Equipo)
- **Regla Estricta Hexagonal:** El dominio no depende de librerías.
- **Ley del Equipo (Pragmatismo):** Se permite anotar los campos de la Entidad de Dominio con `@NotNull` o `@Size` si esto ahorra escribir decenas de validaciones manuales en el constructor, siempre que el equipo tenga configurado el validador a nivel de programa. 

**Ejemplo en un Command:**
```java
package com.empresa.logistica.gestionusuarios.application.command;

import jakarta.validation.constraints.NotBlank;

public record ActivarUsuarioCommand(
    @NotBlank(message = "El ID de usuario no puede estar vacío")
    String usuarioId,
    
    @NotBlank(message = "Debe proporcionar un motivo")
    String motivo
) {}
```
