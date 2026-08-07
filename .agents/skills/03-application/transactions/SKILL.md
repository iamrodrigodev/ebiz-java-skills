---
name: transactions
description: Guia para el manejo de transaccionalidad con @Transactional.
---

# Guia de Transaccionalidad

**Objetivo:** Garantizar la atomicidad y consistencia de las operaciones orquestadas por los Casos de Uso. 

## Reglas de Implementacion y Arquitectura

1. La transaccionalidad se gobierna exclusivamente a nivel de Caso de Uso (Application Service), ya que representa la frontera de la orquestacion del negocio.
2. Todo servicio que realice escrituras o modificaciones debe marcarse con `@Transactional` en su clase o metodo.
3. Los servicios de solo lectura deben marcarse con `@Transactional(readOnly = true)`.

## Lo que SI debes hacer (Buenas Practicas)

```java
import org.springframework.transaction.annotation.Transactional;

@Transactional
public class EliminarLogicamenteUsuarioService implements EliminarLogicamenteUsuarioUseCase {
    // ... inyeccion de puertos
    
    @Override
    public void eliminarLogicamenteUsuario(EliminarLogicamenteUsuarioCommand command) {
        Usuario usuario = cargarUsuarioPorIdPort.cargarPorId(new UsuarioId(command.usuarioId()));
        // mutacion del dominio
        usuario.eliminarLogicamente(LocalDateTime.now(), new ActorAuditoriaId(command.actorId()));
        // persistencia
        guardarUsuarioPort.guardar(usuario);
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Colocar anotaciones transaccionales en los controladores REST.
- Colocar anotaciones transaccionales en el Dominio (rompe el aislamiento).
- Olvidar la anotacion transaccional en un flujo que guarde multiples agregados.

## Instrucciones Especificas para Agentes IA

- Siempre que crees un `*Service` en la capa de aplicacion, evalua si requiere modificacion de datos. De ser asi, coloca la anotacion `@Transactional` de Spring.
- Si el servicio es de pura lectura, asegúrate de colocar `@Transactional(readOnly = true)`.
