---
name: spring-ecosystem
description: Mejores practicas para la integracion de Spring Boot respetando la Arquitectura Hexagonal.
---

# Guia de Ecosistema Spring Boot

**Objetivo:** Utilizar el inmenso poder de Spring Framework (Inyeccion de Dependencias, Transacciones, REST, Datos) como un detalle periferico en lugar de acoplarlo como cerebro maestro del sistema.

## Reglas de Implementacion y Arquitectura

1. **Aislamiento en Configuracion Central:** El modelo indica no inundar las clases de Application o Domain con `@Component`, `@Service`, o `@Autowired`. Las clases de negocio son Puras (POJOs), se ensamblan e inyectan desde archivos estaticos en el paquete `config`.
2. Las unicas excepciones a la regla son la capa web (Controladores con `@RestController`), Excepciones Globales (`@RestControllerAdvice`) y Persistencia (`JpaRepository`), debido a la acentuada dependencia con los protocolos subyacentes.

## Lo que SI debes hacer (Buenas Practicas)

```java
package pe.com.mcalderon.logistica.identidades.gestionusuarios.infrastructure.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

// El Ensamblador de Spring (El unico archivo consciente de las piezas del rompecabezas)
@Configuration
public class GestionUsuariosConfig {

    @Bean
    public CrearUsuarioRestMapper crearUsuarioRestMapper() {
        return new CrearUsuarioRestMapper();
    }

    @Bean
    public UsuarioPersistenceAdapter usuarioPersistenceAdapter(
        UsuarioJpaRepository usuarioJpaRepository,
        UsuarioPersistenceMapper usuarioPersistenceMapper
    ) {
        return new UsuarioPersistenceAdapter(usuarioJpaRepository, usuarioPersistenceMapper);
    }

    @Bean
    public CrearUsuarioUseCase crearUsuarioUseCase(
        ConsultarUsuarioPort consultarUsuarioPort,
        GuardarUsuarioPort guardarUsuarioPort,
        Clock clock
    ) {
        // Enlaza las implementaciones puras inyectando los Fakes / Ports via constructor
        return new CrearUsuarioService(consultarUsuarioPort, guardarUsuarioPort, clock);
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Abusar de `@Autowired` en las propiedades (Field Injection). Siempre prioriza Inyeccion de Constructor que protege la inmutabilidad (`private final`).

```java
// ANTI-PATRON RECHAZADO: Field Injection + Contaminacion de Capa Aplicacion
@Service
public class CrearUsuarioService {
    @Autowired // MALA PRACTICA
    private GuardarUsuarioPort puerto;
}
```

## Instrucciones Especificas para Agentes IA

- Cada vez que crees un componente (Service, Mapper, Adapter) en este proyecto, asegurate de obviar por defecto los decoradores como `@Component` y agregarlos en la clase `@Configuration` del modulo.
- El objeto `Clock` de Java `java.time` siempre debera configurarse y devolverse como un `@Bean` desde aqui para poder hacer simulaciones temporales correctas (`Clock.fixed()`) durante el testeo.
