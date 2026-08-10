---
name: arquitectura-hexagonal
description: "Aislamiento del dominio, puertos (In/Out) y dependencias hacia adentro."
---

# Arquitectura Hexagonal y Puertos

**Definición:**
El centro de la aplicación (Dominio + Application) no sabe nada sobre cómo interactúa con el mundo exterior (Base de datos, HTTP, colas).

**Reglas Inquebrantables:**
1. **[OBLIGATORIO] Dominio Primero:** El dominio se construye de forma pura. No depende de Spring, JPA, REST ni de Application. Todo el conocimiento de negocio está aquí encapsulado.
2. **[OBLIGATORIO] Dependencia hacia adentro:** Los adapters (Infraestructura) son los que dependen de Application. Ellos implementan los contratos (Port-Out) definidos por Application. Application nunca conoce a los adapters, ni a los Repositories de Spring Data.
3. **[PROHIBIDO] Fugas Tecnológicas:** Application y Dominio no llevan NINGUNA anotación tecnológica (`@Service`, `@Autowired`, `@Entity`, `@Table`, `@RestController`).

**Evidencia en el Proyecto el proyecto:**
- El `ConsultarPersonaPort` es una interfaz en Application. 
- El `ConsultarPersonaAdapter` es una clase en Infraestructura que implementa esa interfaz e inyecta el `PersonaReadRepository` (tecnología Spring Data).
