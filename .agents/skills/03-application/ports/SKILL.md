---
name: ports
description: Guia de diseno y separacion de Puertos de Entrada y Salida.
---

# Guia de Puertos (Input/Output Ports)

**Objetivo:** Los puertos definen los contratos mediante los cuales la aplicacion interactua con el mundo exterior. Los Puertos de Entrada definen "que puede pedirle el mundo a mi aplicacion", mientras que los Puertos de Salida definen "que necesita mi aplicacion del mundo".

## Reglas de Implementacion y Arquitectura

1. Los puertos de entrada y salida no se conocen entre si; el unico punto de union es el Caso de Uso (Application Service).
2. Los puertos de salida se disenan desde las necesidades del caso de uso, no desde la estructura de la base de datos (no son simples repositorios CRUD).
3. Una misma implementacion tecnica (Adaptador) puede satisfacer multiples puertos pequenos (Interface Segregation Principle).

## Lo que SI debes hacer (Buenas Practicas)

```java
// Puerto de Entrada: Define el contrato hacia el Controlador REST
public interface ConsultarUsuarioUseCase {
    ConsultarUsuarioResult consultarUsuario(UsuarioId usuarioId);
}

// Puerto de Salida: Define una capacidad externa especifica
public interface ConsultarOtroUsuarioConCorreoPort {
    boolean existeOtroUsuarioConCorreo(CorreoElectronico correo, UsuarioId usuarioIdExcluido);
}
```

## Lo que NO debes hacer (Anti-patrones)

```java
// ERROR: Diseno de puerto basado en tabla de datos (CRUD), rompe el concepto de "Capacidad"
public interface UsuarioRepositoryPort {
    void save(Usuario u);
    Usuario findById(Long id);
    void delete(Usuario u);
    List<Usuario> findAll();
}
```

## Instrucciones Especificas para Agentes IA

- Siempre que crees un puerto de salida, debes nombrarlo describiendo la accion especifica que realiza (ej. `CargarUsuarioPorIdPort`, `GuardarUsuarioPort`), terminando con el sufijo `Port`.
- Nunca agrupes todas las operaciones de persistencia en una unica interfaz gigantesca. Aplica segregacion de interfaces (ISP).
