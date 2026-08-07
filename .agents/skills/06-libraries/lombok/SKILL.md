---
name: lombok
description: Estandar restrictivo para el uso de la libreria Lombok en el proyecto.
---

# Guia de Uso de Lombok

**Objetivo:** Reducir la verbosidad de Java de forma controlada sin comprometer las reglas de proteccion de invariantes ni el diseno del Dominio Rico.

## Reglas de Implementacion y Arquitectura

1. **Aislamiento de Mapeo y Creacion:** Lombok NUNCA debe generar constructores vacios o constructores que inicialicen indiscriminadamente todos los campos de una clase de Dominio, ya que puentea la fabrica de inicializacion estricta de la Arquitectura Hexagonal.
2. Esta estrictamente prohibido el uso de `@Data` en objetos de Dominio. 
3. Lombok se permite exclusivamente como una herramienta estetica para Getters puntuales u objetos chatos de transferencia.

## Lo que SI debes hacer (Buenas Practicas)

```java
import lombok.Getter;

// Solo en DTOs, Commands, y Value Objects simples donde no haya reglas de mutacion
@Getter
public class CrearUsuarioCommand {
    private final Long personaId;
    private final String correo;
    
    // El constructor se sigue escribiendo a mano o delegando estrictamente
}
```

## Lo que NO debes hacer (Anti-patrones)

- Usar `@NoArgsConstructor` o `@AllArgsConstructor`. Especialmente critico porque un objeto vacio rompe el modelo DDD al permitir que nazca en estados invalidos.
- Usar `@Setter` en agregados o clases, induciendo al anti-patron de entidades anemicas.

```java
// ERROR FATAL DE ARQUITECTURA
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Usuario {
    // Si alguien hace 'new Usuario()', todo nace nulo burlando las reglas del negocio.
}
```

## Instrucciones Especificas para Agentes IA

- Antes de incluir importaciones de `lombok.*`, asegurate de que la clase no pertenece a `domain/model`. Si pertenece al dominio, no uses Lombok para acortar codigo, escribe el constructor y los getters funcionales a mano de acuerdo a las directrices de inmutabilidad.
- En capas de Infraestructura o Application, asegurate de nunca incluir `@NoArgsConstructor` para clases que representen Command u objetos inmutables.
