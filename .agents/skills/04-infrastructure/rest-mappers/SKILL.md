---
name: rest-mappers
description: Guia de traduccion manual entre el Dominio y el Protocolo REST.
---

# Guia de Mappers REST

**Objetivo:** Implementar traductores que garanticen el aislamiento de los Casos de Uso. Su trabajo es asegurar que el JSON que ingresa se convierta estrictamente en el `Command` inmutable de Java, y que la respuesta de Dominio pase a un `Response` para JSON.

## Reglas de Implementacion y Arquitectura

1. **Rechazo a librerias magicas:** Segun el PDF arquitectonico base del equipo, estos mapeos se realizan manualmente en clases final, sin usar frameworks automaticos (como MapStruct) para evitar ocultar magia negra.
2. **Principio de minima exposicion:** El Request recibe unicamente lo que necesita del cliente, y el Response devuelve unicamente la porcion de la data no-sensible.

## Lo que SI debes hacer (Buenas Practicas)

```java
import java.util.Objects;

public final class CrearUsuarioRestMapper {
    
    // Request (Entrada web) -> Command (Objeto interno de app)
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
    
    // ID del Dominio puro -> Response (Salida Web)
    public CrearUsuarioResponse aResponse(UsuarioId usuarioId) {
        Objects.requireNonNull(usuarioId, "UsuarioId no puede ser nulo");
        return new CrearUsuarioResponse(usuarioId.valor());
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Retornar campos confidenciales (como contraseñas, tokens crudos o data auditoria innecesaria) mapeados por defecto en las respuestas HTTP.
- Anadir operaciones logicas u operaciones matematicas adentro de los metodos de mapeo.

## Instrucciones Especificas para Agentes IA

- Crea Mappers manuales usando la palabra reservada `final class`.
- Incorpora siempre el uso intensivo de `Objects.requireNonNull()` para validar la sanidad del DTO de entrada antes de generar el comando.
