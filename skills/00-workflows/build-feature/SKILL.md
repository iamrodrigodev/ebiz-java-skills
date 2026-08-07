---
name: build-feature
description: Asistente orquestador (Master Skill) para construir una capacidad/funcionalidad completa End-to-End siguiendo el manual oficial.
---

# Master Skill: Desarrollo End-to-End de una Funcionalidad

Esta es la skill más poderosa del sistema. Sirve como un Orquestador. Cuando el usuario te pida construir una nueva capacidad (ej. "Construye la Gestión de Libros" o "Crea la funcionalidad de Carrito"), **debes seguir estrictamente los siguientes pasos secuenciales**, consultando las sub-skills mencionadas cuando tengas dudas.

**REGLA DE ORO:** No escribas todo el código de golpe. Haz pausas estratégicas (preguntando al humano si está de acuerdo con el avance) después de la Fase 1 y la Fase 2.

## FASE 1: Diseño del Dominio (El Corazón)
1. **Entrevista de Negocio:** Hazle preguntas al usuario sobre las reglas de vigencia, estados y acciones que sufre la Entidad (apoyate en la skill `domain-model`).
2. **Generación del Modelo Rico:**
   - Crea la Entidad en `domain/model` con fábrica de creación segura y constructores privados.
   - Crea *Value Objects* para campos que requieran tipado fuerte. Lee la skill `value-objects`.
3. **Pruebas del Dominio:**
   - Crea las pruebas unitarias sin mocks en `domain/model` verificando las invariantes. Lee la skill `domain-testing`.

## FASE 2: Diseño de la Aplicación (La Orquestación)
1. **Generación de Commands y Ports:**
   - Crea los `Command` inmutables (con Jakarta Validation) y los puertos (`port.in` y `port.out`). Lee la skill `ports`.
2. **Generación de Casos de Uso:**
   - Crea los `Service` que implementen los `port.in`. Recuerda: no usan `@Service`. Lee la skill `usecases`.
3. **Pruebas de Aplicación:**
   - Crea pruebas unitarias usando `Mockito` para validar la orquestación. Lee la skill `application-testing`.

## FASE 3: Infraestructura REST y Persistencia
1. **Controladores y Mappers (REST):**
   - Crea el `Controller` con endpoints semánticos, junto con sus `RequestDTO`, `ResponseDTO` y su `Mapper`. Lee la skill `rest-mappers`.
   - Asegúrate de que el `GlobalExceptionHandler` esté configurado para atrapar errores (lee la skill `global-exception-handler`).
2. **Base de Datos (Persistencia):**
   - Crea la `JpaEntity`, el `JpaRepository` y el `Adapter` que implemente el `port.out`.
   - Verifica la configuración de SQL Server si es necesario (lee `sql-server-config`).

## FASE 4: Validación End-to-End
1. **Pruebas de Integración:**
   - Crea pruebas E2E con `@SpringBootTest` y `MockMvc` validando flujos de éxito (ej. usuario creado) y flujos de error (ej. correo duplicado, entidad no encontrada). Lee la skill `e2e-testing`.

## Instrucciones para el Agente (LLM)
- Actúa como un Arquitecto de Software y Tech Lead.
- Cuando inicies este workflow, imprime en pantalla un Checklist (Markdown) con estas 4 Fases para que el usuario sepa en qué paso van.
- Utiliza checkmarks ( [x] ) a medida que avances de fase.


