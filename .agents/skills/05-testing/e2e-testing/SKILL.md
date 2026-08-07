---
name: e2e-testing
description: Estandar para pruebas End-to-End validando integracion completa.
---

# Guia de Pruebas E2E (End-to-End)

**Objetivo:** Las pruebas E2E verifican la integridad total del flujo cruzando TODAS las capas de la arquitectura Hexagonal (Controller -> Input Port -> Use Case -> Domain -> Output Port -> Adapter -> DB). Esto valida que tanto el codigo como las anotaciones JSON/Validacion y las politicas del GlobalExceptionHandler interactuen correctamente.

## Reglas de Implementacion y Arquitectura

1. Se usa Postman (Manual) y MockMvc/RestAssured (Automatico) simulando las llamadas exactas del exterior a los endpoints expuestos (`/api/usuarios`).
2. Se evaluan todas las casuisticas de exito y fracaso reportadas por la arquitectura.
3. Se verifican estrictamente los "Payloads" y codigos de estado HTTP arrojados.

## Lo que SI debes hacer (Buenas Practicas)

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@SpringBootTest
@AutoConfigureMockMvc
class UsuarioE2ETest {

    @Autowired
    private MockMvc mockMvc;

    // 1. Probar Happy Path (Integracion Correcta)
    @Test
    void insertarUsuarioValido_DeberiaCrearExitosamente() throws Exception {
        String requestValido = """
            {
                "personaId": 1,
                "nombre": "Miguel Calderon",
                "correo": "miguel.calderon@empresa.com",
                "origen": "I",
                "tipoCuenta": "P",
                "fechaInicioVigencia": "2026-07-30",
                "actorCreacionId": 1
            }
        """;

        mockMvc.perform(post("/api/usuarios")
                .contentType(MediaType.APPLICATION_JSON)
                .content(requestValido))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.usuarioId").exists());
    }

    // 2. Probar Rutas de Error Logico
    @Test
    void insertarUsuarioConCorreoExistente_DeberiaLanzarConflicto() throws Exception {
        String requestDuplicado = "{ ... mismo payload anterior ... }";

        // Pre-insertar uno manualmente o hacer una llamada repetida
        mockMvc.perform(post("/api/usuarios").contentType(MediaType.APPLICATION_JSON).content(requestDuplicado));

        // Segunda llamada
        mockMvc.perform(post("/api/usuarios")
                .contentType(MediaType.APPLICATION_JSON)
                .content(requestDuplicado))
                .andExpect(status().isConflict())
                .andExpect(jsonPath("$.codigo").value("CORREO_ELECTRONICO_YA_REGISTRADO"));
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Enviar objetos Java encriptados hacia las llamadas de MockMvc. Usa puramente representaciones String/JSON para forzar el paso real de mapeo e instanciacion.
- Burlar o mockear las capas subyacentes con `@MockBean` dentro del End-to-End. En un test de Integracion E2E se desea probar la cañería real completa conectandose a una base de datos embebida o real.

## Instrucciones Especificas para Agentes IA

- Siempre formula las pruebas de integracion utilizando `@AutoConfigureMockMvc` dentro de `@SpringBootTest`.
- Mapea las aserciones de respuesta utilizando JsonPath (`jsonPath("$.codigo")`) ya que la convencion de este proyecto dicta manejar fallos customizados via `GlobalExceptionHandler` envolviendo `ApiErrorResponse`.
