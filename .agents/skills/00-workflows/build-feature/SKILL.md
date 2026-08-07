---
name: build-feature
description: Asistente orquestador (Master Skill) para construir un Bounded Context End-to-End siguiendo el manual oficial y previniendo alucinaciones.
---

# Master Skill: Desarrollo End-to-End

**Objetivo:** Guiar a la IA (o desarrollador humano) paso a paso en la implementacion completa de una nueva funcionalidad cruzando todas las capas de Arquitectura Hexagonal y respetando los rigurosos canones de inmutabilidad del equipo.

## Flujo Estricto de Orquestacion

Cuando el usuario te pida construir una nueva capacidad (ej. "Construye el modulo de Vehiculos"), debes cumplir estrictamente las Fases de este Pipeline Secuencial.

**REGLA DE ORO:** Realiza pausas estratégicas (solicita confirmación al usuario o pregunta detalles funcionales) entre Fase 1 y Fase 2.

---

### FASE 1: Diseño del Dominio Puro (El Corazón)
*Lectura obligatoria:* `02-domain/domain-model`, `02-domain/value-objects`, `02-domain/cross-cutting-domain`
1. **Modelado Rico:** Define los `Value Objects` (ej. Matricula) blindando la data en su "punto de verdad local". Construye los `Enums` estaticos de clasificacion o estado.
2. **Auditoria y Nacimiento:** Haz que la Entidad de negocio herede de `EntidadAuditada`. Define su Constructor con visibilidad `private` y añade las fábricas estáticas `crear()` y `rehidratar()`.
3. **Pruebas de Dominio (Testing):** (Consulta `05-testing/domain-testing`). Diseña tests parametrizados usando JUnit puro (¡Cero `@SpringBootTest`!). Valida invariantes y mutaciones sin levantar contexto.

### FASE 2: Diseño de la Aplicación (La Orquestación)
*Lectura obligatoria:* `03-application/usecases`, `03-application/ports`
1. **Contratos e Intenciones:** Define el DTO inmutable `Command`. Crea los contratos de los Puertos de Entrada (`*UseCase`) y Salida (`*Port`), diseñandolos segun la accion y capacidad.
2. **Servicio y Transaccionalidad:** Construye el `Application Service` que une y orquesta la validacion previa al Dominio, instanciando la Entidad para despues persistirla. Utiliza inyeccion manual en constructor y añade `@Transactional` segun corresponda (Consulta `03-application/transactions`).
3. **Pruebas de Orquestacion:** (Consulta `05-testing/application-testing`). ¡PROHIBIDO USAR MOCKITO! Implementa clases `*Fake` locales (en memoria) para simular los puertos de salida y usa `Clock.fixed` para dominar la asercion temporal de datos.

### FASE 3: Capa de Infraestructura (La Frontera REST y SQL)
*Lectura obligatoria:* `04-infrastructure/rest-api`, `04-infrastructure/sql-server-config`, `04-infrastructure/persistence-entities`
1. **Controladores y Mapeos:** Expone el Input Adapter en `UsuarioController`, acompáñalo de sus propios DTOs JSON (`Request`/`Response` con `@Valid`). Construye un Mapper final manual sin frameworks automaticos para inyectarlo hacia el Command.
2. **Integridad de Base de Datos:** Configura las anotaciones JPA unicamente en el `UsuarioJpaEntity` y levanta su propio `UsuarioPersistenceMapper`. Anade scripts migratorios T-SQL (`V0XX.sql`).

### FASE 4: Cierre E2E y Gestion de Errores Globales
*Lectura obligatoria:* `04-infrastructure/global-exception-handler`, `05-testing/e2e-testing`, `06-libraries/spring-ecosystem`
1. **Manejo Centralizado:** Anade soporte a los nuevos fallos semanticos generados dentro del `GlobalExceptionHandler` retornando siempre un payload customizado `ApiErrorResponse`.
2. **Integracion E2E:** Levanta el contexto y asegura el ciclo total utilizando Postman/MockMvc evaluando Happy Path (HTTP 201) y Negativas (HTTP 409, 404).
3. **Inyeccion Maestra:** Registra formalmente todos los adaptadores, servicios y mappers que creaste en el ciclo manual dentro del archivo `@Configuration` (`Gestion*Config.java`).

## Instrucciones Especificas para Agentes IA
- Si detectas inconsistencias en la solicitud del humano, deten el ciclo e invoca preguntas precisas.
- Imprime siempre este checklist en Markdown con marcas `[ ]` o `[x]` al iniciar el flujo para guiar la lectura de las iteraciones conversacionales.

