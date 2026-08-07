---
name: domain-testing
description: Estandar para pruebas puras de Dominio en JUnit 5 usando invariantes.
---

# Guia de Pruebas de Dominio

**Objetivo:** Aislar y probar todas las reglas criticas del negocio utilizando especificaciones y parametrizaciones ejecutables rapidas y confiables (Cero mocks, cero integracion con frameworks tecnologicos).

## Reglas de Implementacion y Arquitectura

1. Las reglas de Dominio NO pueden instanciar Mocks ni Fakes externos; solamente prueban Objetos de Dominio reales y validan sus cambios de estado internos y excepciones.
2. Cada rama logica y de invariantes en constructores, objetos de valor y fabricas debe tener un escenario de fallo y uno de exito.
3. Se prefieren los tests parametrizados (`@ParameterizedTest`) para ejecutar rapidamente amplios espectros de condiciones estaticas.

## Lo que SI debes hacer (Buenas Practicas)

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.NullAndEmptySource;
import org.junit.jupiter.params.provider.ValueSource;
import static org.junit.jupiter.api.Assertions.*;

class CorreoElectronicoTest {

    // 1. Probar fallos de invariantes con fuentes de datos nulas o vacias
    @ParameterizedTest
    @NullAndEmptySource
    @ValueSource(strings = {" ", "  "})
    void noDebePermitirCorreoNuloOVacio(String valor) {
        assertThrows(IllegalArgumentException.class, () -> new CorreoElectronico(valor));
    }

    // 2. Probar logica de validacion compleja (Regex) con fuentes de datos erroneas
    @ParameterizedTest
    @ValueSource(strings = {"miguel", "miguel@", "@empresa.com", "miguel empresa@empresa.com"})
    void noDebePermitirFormatosInvalidos(String valor) {
        assertThrows(IllegalArgumentException.class, () -> new CorreoElectronico(valor));
    }

    // 3. Probar comportamientos y metodos funcionales
    @Test
    void debeNormalizarEspaciosYMayusculas() {
        CorreoElectronico correo = new CorreoElectronico(" MIGUEL@EMPRESA.COM ");
        assertEquals("miguel@empresa.com", correo.valor());
    }
}
```

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.Arguments;
import org.junit.jupiter.params.provider.MethodSource;
import java.util.stream.Stream;

class PoliticaVigenciaTest {

    // Uso agresivo de parametros para reglas cruzadas (Ej: Vigencia se calcula segun Origen y Tipo de Cuenta)
    @ParameterizedTest
    @MethodSource("casosDeVigencia")
    void probarPeriodosSegunVariables(OrigenUsuario origen, TipoCuenta tipo, int duracionEsperada) {
        LocalDate inicio = LocalDate.of(2026, 1, 1);
        PeriodoVigencia periodo = politica.calcular(inicio, origen, tipo);
        
        LocalDate esperado = inicio.plusDays(duracionEsperada - 1L);
        assertEquals(esperado, periodo.fechaFin());
    }

    private static Stream<Arguments> casosDeVigencia() {
        return Stream.of(
            Arguments.of(OrigenUsuario.INTERNO, TipoCuenta.PERSONAL, 365),
            Arguments.of(OrigenUsuario.EXTERNO, TipoCuenta.ADMINISTRACION, 10)
        );
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Ejecutar las pruebas usando frameworks como `@SpringBootTest`, reduciendo enormemente la velocidad del set de pruebas.
- Omitir mensajes claros en los metodos (nombramiento opaco como `test1()`).

## Instrucciones Especificas para Agentes IA

- Siempre que te pidan escribir pruebas para el DOMINIO puro, apalancate intensamente en los atributos `@ParameterizedTest` junto con `@ValueSource` o `@MethodSource` de JUnit 5.
- La nomenclatura estandar a seguir debe ser `debe[HacerAlgo]()` o `noDebe[HacerAlgo]()` (Ej: `noDebePermitirCorreoNulo()`).
