---
name: lombok
description: Guía oficial del equipo sobre cómo y dónde utilizar Lombok en la arquitectura.
---

# Guía de Implementación: Lombok

El equipo ha decidido abrazar las ventajas de **Lombok** para reducir el código repetitivo (boilerplate), siempre y cuando no se rompan las reglas del diseño rico (DDD).

## 1. Uso en la Capa de Dominio (`domain/model`)
- **Permitido:** `@Getter` (a nivel de clase), `@Builder` (con acceso restringido).
- **Prohibido:** `@Setter`, `@Data`, `@NoArgsConstructor` y `@AllArgsConstructor`. ¡Un modelo de dominio rico NUNCA expone setters públicos ni depende de constructores inseguros!
- **Propósito:** Facilitar la creación del objeto (Builder o Constructor) y la lectura de sus atributos, manteniendo el comportamiento encapsulado en verbos de negocio.

## 2. Uso en la Capa de Aplicación (`application/command` y `application/dto`)
- **Permitido:** En los Commands (si se usan clases regulares) se permite `@Data`, `@Value` o `@Builder`. *Nota: Si se usan `record` de Java 14+, Lombok no es necesario aquí.*

## 3. Uso en la Capa de Infraestructura (`adapter.out.persistence.entity`)
- **Permitido:** `@Getter`, `@Setter`, `@Builder`.
- **Prohibido:** `@Data`, `@EqualsAndHashCode`, `@NoArgsConstructor`, `@AllArgsConstructor`.
- **Razón:** `@Data` implementa un `hashCode()` que incluye todos los campos. En JPA, si una entidad cambia de estado (ej. un ID auto-generado), su HashCode cambia, lo cual rompe colecciones como `HashSet` o `HashMap` y causa bugs críticos en Hibernate. Adicionalmente, `@AllArgsConstructor` es frágil ante cambios de orden en las propiedades y `@NoArgsConstructor` debe evitarse en favor de constructores manuales `protected` requeridos por JPA.
