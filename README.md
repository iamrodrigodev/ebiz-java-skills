# Base de Conocimiento: Arquitectura Hexagonal y DDD

Bienvenido al repositorio oficial de estandares y reglas arquitectonicas del equipo (`ebiz-java-skills`). 

Este repositorio funciona como la **unica fuente de verdad** para el desarrollo de software basado en Arquitectura Hexagonal y Domain-Driven Design (DDD) utilizando Java y Spring Boot. Su proposito es garantizar que todo el equipo (y las herramientas de IA asistida) mantengan un codigo uniforme, predecible y altamente desacoplado.

---

## Indice de Arquitectura (De Adentro hacia Afuera)

La documentacion esta organizada reflejando las capas de nuestra arquitectura. Antes de implementar un nuevo modulo, asegurate de consultar la guia correspondiente:

### 00. Flujo de Trabajo
*   **`build-feature`**: Orquestador paso a paso que define en que orden se deben construir los componentes de una nueva funcionalidad.

### 01. Fundamentos
*   **`architecture-review`**: Que esta prohibido y que esta permitido en las importaciones.
*   **`dependency-rules`**: Direccion del flujo de dependencias entre capas.
*   **`package-structure`**: Arbol de directorios basado en *Vertical Slicing* (Agrupacion por capacidad de negocio, no por tecnologia).

### 02. Dominio Puro (El Corazon del Negocio)
*   **`domain-model`**: Como diseñar Entidades ricas, proteger invariantes y evitar el anti-patron de entidades anemicas.
*   **`value-objects`**: Reemplazo de primitivos genericos por objetos fuertemente tipados.
*   **`cross-cutting-domain`**: Gestion de auditoria mediante herencia (`EntidadAuditada`).

### 03. Capa de Aplicacion (La Orquestacion)
*   **`ports`**: Definicion de contratos de Entrada (`*UseCase`) y Salida (`*Port`).
*   **`usecases`**: Creacion de Servicios de Aplicacion para coordinar el Dominio sin logica de negocio.
*   **`transactions`**: Gobierno del estado transaccional (`@Transactional`).

### 04. Capa de Infraestructura (La Frontera Externa)
*   **`rest-api`**: Controladores web y codigos de estado semanticos.
*   **`rest-mappers`**: Aislamiento del Dominio de los DTOs de Request/Response.
*   **`global-exception-handler`**: Intercepcion de errores de negocio y transformacion a `ApiErrorResponse`.
*   **`persistence-entities`**: Representacion tecnica de tablas (JPA) sin logica.
*   **`persistence-repositories`**: Interfaces `JpaRepository`.
*   **`persistence-mappers`**: Traduccion bidireccional entre JPA y Dominio.
*   **`persistence-adapters`**: Implementacion material de los Puertos de Salida.
*   **`sql-server-config`**: application.properties, dialectos y migraciones DDL.

### 05. Testing (Piramide de Pruebas)
*   **`domain-testing`**: JUnit 5 puro con invariantes, cero dependencias.
*   **`application-testing`**: Uso de simuladores en memoria (`Fakes`), **prohibido usar Mockito**.
*   **`e2e-testing`**: Integracion total usando MockMvc simulando requests JSON reales.

### 06. Librerias y Ecosistema
*   **`lombok`**: Prohibicion estricta de constructores vacios en el dominio.
*   **`spring-ecosystem`**: Inyeccion manual de componentes via `@Configuration`.
*   **`validation`**: Jakarta Validation exclusivo para la entrada HTTP.

---

## ¿Como utilizar esta documentacion?

### Para el Equipo de Desarrollo (Humanos)
Este repositorio es tu guia de consulta. No necesitas aprenderlo todo de memoria. Si hoy tienes la tarea de crear un endpoint, dirígete a `04-infrastructure/rest-api` para ver los "Do's and Don'ts" (Buenas practicas y Anti-patrones) aprobados por el equipo.

### Para el Asistente de Codigo (IA)
Este repositorio esta configurado automaticamente como un *Workspace Customization*. Cuando interactues con un Agente IA en el repositorio, la IA leera automaticamente las reglas ubicadas en `.agents/skills` y se asegurara de que el codigo generado cumpla al 100% con los estandares definidos aqui.

Simplemente pidele a la IA que programe apoyandose en estas reglas. Ejemplo:
> *"Crea la entidad Vehiculo respetando las reglas de la skill `domain-model` y `value-objects`."*
