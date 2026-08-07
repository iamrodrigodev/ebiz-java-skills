---
name: domain-testing
description: Guía de pruebas unitarias para el dominio (JUnit puro sin Mocks).
---

# Guía de Implementación: Pruebas de Dominio

Las reglas de dominio se prueban con pruebas unitarias aisladas usando **JUnit puro**. Actúan como especificaciones ejecutables. **NO SE USAN MOCKS (Mockito).**

## 1. Pruebas de Políticas (Parameterized Tests)

Las políticas complejas (como calcular vigencia por tipo de cuenta) deben probarse exhaustivamente pasando múltiples parámetros.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.Arguments;
import org.junit.jupiter.params.provider.MethodSource;
import static org.junit.jupiter.api.Assertions.assertEquals;

class PoliticaVigenciaUsuarioEstandarTest {
    private final PoliticaVigenciaUsuario politica = new PoliticaVigenciaUsuarioEstandar();

    @ParameterizedTest
    @MethodSource("casosDeVigencia")
    void debeCalcularLaVigenciaSegunOrigenYTipoDeCuenta(OrigenUsuario origenUsuario, TipoCuenta tipoCuenta, int duracionDias) {
        LocalDate fechaInicio = LocalDate.of(2026, 1, 1);
        PeriodoVigencia periodo = politica.calcular(fechaInicio, origenUsuario, tipoCuenta);
        
        LocalDate fechaFinEsperada = fechaInicio.plusDays(duracionDias - 1L);
        assertEquals(fechaFinEsperada, periodo.fechaFin());
    }

    private static Stream<Arguments> casosDeVigencia() {
        return Stream.of(
            Arguments.of(OrigenUsuario.INTERNO, TipoCuenta.PERSONAL, 365),
            Arguments.of(OrigenUsuario.EXTERNO, TipoCuenta.PERSONAL, 90)
            // ... otras combinaciones
        );
    }
}
```

## 2. Pruebas de Value Objects (Invariantes Locales)

Validar que el Value Object proteja su propia verdad local.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.NullAndEmptySource;
import org.junit.jupiter.params.provider.ValueSource;

class CorreoElectronicoTest {
    @ParameterizedTest
    @ValueSource(strings = {"miguel", "miguel@", "@empresa.com"})
    void noDebePermitirFormatosInvalidos(String valor) {
        assertThrows(IllegalArgumentException.class, () -> new CorreoElectronico(valor));
    }
}
```

## 3. Pruebas de Comportamiento de Entidades

Llamar a los métodos de dominio verificando la modificación de estados (incluyendo la auditoría).

```java
@Test
void debeEliminarLogicamenteAlUsuario() {
    Usuario usuario = crearUsuarioPersonalInterno(); // Método de utilidad privado
    LocalDateTime fechaEliminacion = LocalDateTime.of(2026, 7, 31, 9, 15);
    ActorAuditoriaId actorEliminacionId = new ActorAuditoriaId(2L);
    
    usuario.eliminarLogicamente(fechaEliminacion, actorEliminacionId);
    
    assertTrue(usuario.estaEliminadoLogicamente());
    assertFalse(usuario.estaRegistrado());
}
```
