---
name: spring-ecosystem
description: Guía de adopción del ecosistema Spring Boot en la arquitectura.
---

# Guía de Implementación: Ecosistema Spring Boot

Aunque la Arquitectura Hexagonal aísla la lógica de negocio (Dominio) de los frameworks, **este proyecto adopta Spring Boot como su esqueleto principal**. Los desarrolladores y agentes de IA deben aprovechar al máximo las herramientas de Spring en las capas permitidas.

## Reglas de Uso de Spring

1. **Inyección de Dependencias (IoC):**
   - Usa la inyección por constructor nativa de Java en TODAS las capas.
   - ¡NO uses `@Autowired` en los campos!
   - Usa las anotaciones de estereotipo (`@RestController`, `@Repository`, `@Component`, `@Configuration`) **únicamente** en la capa de `infrastructure`.

2. **Capa de Aplicación (Casos de Uso):**
   - Para registrar los Casos de Uso en el contexto de Spring sin contaminarlos con `@Service`, el equipo debe usar una clase de `@Configuration` en la capa de `infrastructure` que instancie los servicios puros usando `@Bean`.

3. **Manejo de Errores (@ControllerAdvice):**
   - Los errores lanzados por el dominio (`domain/exception`) o por validaciones deben ser atrapados globalmente en la infraestructura usando un `@RestControllerAdvice`. No ensucies los Controladores con bloques `try-catch`.

## Instrucciones para el Agente (LLM)
- Cuando el usuario pida ayuda para Spring, asume que se refiere a las mejores prácticas de Spring Boot 3.x (Jakarta EE, no Javax).
- Si vas a proponer un nuevo componente técnico (un cliente HTTP, un publicador de eventos), busca siempre la solución nativa de Spring (ej. `RestClient`, `ApplicationEventPublisher`) antes de introducir librerías de terceros.
