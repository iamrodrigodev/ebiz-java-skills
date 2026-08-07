---
name: application-testing
description: Estandar oficial para probar los Casos de Uso (Mocks vs Fakes).
---

# Guia de Pruebas de Aplicacion (Application Testing)

**Objetivo:** Verificar la correcta orquestacion de reglas y comandos dictaminados por el Caso de Uso (Application Service), comprobando que logre coordinar el flujo llamando a los puertos correctos.

## Reglas de Implementacion y Arquitectura

1. **NO usar Mocks:** La directiva estricta de la empresa indica evitar librerias de Mocking automáticas (como Mockito).
2. **Utilizar FAKES en Memoria:** Se construyen implementaciones simuladas (`*PortFake`) dentro de la misma clase del Test, lo cual provee mas flexibilidad y resistencia a refactorizaciones que la fragilidad inherente del espionaje con `verify()` o `when()`.
3. **Controlar el Tiempo:** Se debe fijar el tiempo durante la ejecucion usando un `Clock.fixed`.

## Lo que SI debes hacer (Buenas Practicas)

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import java.time.Clock;
import java.time.Instant;
import java.time.ZoneId;

class CrearUsuarioServiceTest {

    // 1. Declaramos los simuladores de los puertos externos
    private ConsultarPersonaPortFake consultarPersonaPort;
    private GuardarUsuarioPortFake guardarUsuarioPort;
    private CrearUsuarioService service;
    
    @BeforeEach
    void preparar() {
        consultarPersonaPort = new ConsultarPersonaPortFake();
        guardarUsuarioPort = new GuardarUsuarioPortFake(new UsuarioId(100L));
        
        // 2. Inyectamos los fakes en el servicio a testear junto con un Clock congelado
        service = new CrearUsuarioService(
            new ConsultarUsuarioPortFake(),
            guardarUsuarioPort,
            consultarPersonaPort,
            new PoliticaVigenciaUsuarioEstandar(),
            Clock.fixed(Instant.parse("2026-07-24T15:30:00Z"), ZoneId.of("America/Lima"))
        );
    }

    @Test
    void debeCrearYGuardarElUsuario() {
        CrearUsuarioCommand command = crearCommandValido(); // metodo utilitario
        
        UsuarioId resultado = service.crearUsuario(command);
        
        assertEquals(new UsuarioId(100L), resultado);
        // 3. Afirmamos estado contra la memoria de nuestro fake
        assertTrue(guardarUsuarioPort.usuarioGuardado().estaPendienteDeActivacion());
    }

    /* ----------------------------------------------------
     * Construccion del Fake en la misma clase del test
     * ---------------------------------------------------- */
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
        
        // Metodo de soporte para poder auditar el resultado interno
        Usuario usuarioGuardado() { return usuarioGuardado; }
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Importar `org.mockito.*` y usar `@Mock` o `@InjectMocks`. Este patron se rechaza activamente en este proyecto.
- Lanzar test contra Spring Context (anotacion `@SpringBootTest`) aqui. Esta capa se prueba de forma 100% aislada.

## Instrucciones Especificas para Agentes IA

- Siempre que te soliciten probar unitariamente un Caso de Uso, DEBES seguir el patron arquitectonico "Fake". Construye clases `static final` que implementen los Puertos de Salida requeridos.
- Las variables para simular persistencia en el Fake (ej. `private boolean personaExistente`) deben permitir metodos setters personalizados como `configurarPersonaExistente()` para poder forzar flujos negativos en los tests.
