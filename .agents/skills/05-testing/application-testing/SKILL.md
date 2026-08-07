---
name: application-testing
description: Guía de pruebas unitarias para Casos de Uso usando FAKES (¡No Mockito!).
---

# Guía de Implementación: Pruebas de Aplicación (Fakes)

**DECISIÓN CRÍTICA DE ARQUITECTURA:** En esta arquitectura, **NO se utilizan librerías de Mocking (como Mockito)** para probar la capa de aplicación. En su lugar, se construyen implementaciones **"Fake"** de los puertos de salida en memoria dentro de la misma clase de prueba.

Esto garantiza un comportamiento de prueba más robusto, cercano a la realidad, sin acoplar las pruebas a las llamadas exactas de los métodos internos.

## 1. Construcción de Fakes (Simuladores en Memoria)

Al final de tu clase de Test (ej. `CrearUsuarioServiceTest`), implementa las interfaces de los Puertos de Salida.

```java
/*
 * Fake de consulta de personas.
 * sustituye temporalmente al futuro adaptador SQL Server/JPA.
 */
private static final class ConsultarPersonaPortFake implements ConsultarPersonaPort {
    private boolean personaExistente = true;
    private PersonaId personaConsultada;

    @Override
    public boolean existePersona(PersonaId personaId) {
        this.personaConsultada = personaId;
        return personaExistente;
    }

    void configurarPersonaExistente(boolean personaExistente) {
        this.personaExistente = personaExistente;
    }
}

/*
 * Fake de guardado: captura el Usuario que Application intentó persistir
 */
private static final class GuardarUsuarioPortFake implements GuardarUsuarioPort {
    private final UsuarioId usuarioIdGenerado;
    private Usuario usuarioGuardado;

    private GuardarUsuarioPortFake(UsuarioId usuarioIdGenerado) {
        this.usuarioIdGenerado = usuarioIdGenerado;
    }

    @Override
    public UsuarioId guardar(Usuario usuario) {
        this.usuarioGuardado = usuario;
        return usuarioIdGenerado;
    }
    
    Usuario usuarioGuardado() { return usuarioGuardado; }
}
```

## 2. Inyección Manual en el Test

Se inyectan los Fakes directamente por el constructor del Application Service, junto con un `Clock.fixed` para controlar el tiempo.

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

class CrearUsuarioServiceTest {
    private ConsultarPersonaPortFake consultarPersonaPort;
    private GuardarUsuarioPortFake guardarUsuarioPort;
    private CrearUsuarioService service;
    
    @BeforeEach
    void preparar() {
        consultarPersonaPort = new ConsultarPersonaPortFake();
        guardarUsuarioPort = new GuardarUsuarioPortFake(new UsuarioId(100L));
        
        service = new CrearUsuarioService(
            new ConsultarUsuarioPortFake(),
            guardarUsuarioPort,
            consultarPersonaPort,
            new PoliticaVigenciaUsuarioEstandar(),
            Clock.fixed(Instant.parse("2026-07-24T15:30:00Z"), ZoneId.of("America/Lima"))
        );
    }

    @Test
    void debeCrearGuardarYDevolverElIdentificadorDelUsuario() {
        CrearUsuarioCommand command = crearCommandValido();
        
        UsuarioId resultado = service.crearUsuario(command);
        
        assertEquals(new UsuarioId(100L), resultado);
        assertNotNull(guardarUsuarioPort.usuarioGuardado());
        assertTrue(guardarUsuarioPort.usuarioGuardado().estaPendienteDeActivacion());
    }
    
    @Test
    void noDebeGuardarCuandoLaPersonaNoExiste() {
        consultarPersonaPort.configurarPersonaExistente(false);
        
        assertThrows(PersonaNoExisteException.class, () -> service.crearUsuario(crearCommandValido()));
        assertNull(guardarUsuarioPort.usuarioGuardado());
    }
}
```
