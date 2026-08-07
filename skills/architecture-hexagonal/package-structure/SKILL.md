---
name: hexagonal-architecture
description: Skill para implementar Arquitectura Hexagonal y Vertical Slicing según las directrices del arquitecto.
---

# Arquitectura Hexagonal y Vertical Slicing

Esta skill define las reglas de diseño y estructura de paquetes para los proyectos, basándose en los principios de **Screaming Architecture**, **Vertical Slicing**, **Arquitectura Hexagonal** y **Diseño Orientado al Dominio (DDD)**.

## 1. Principios Generales

- **Screaming Architecture**: La estructura principal del proyecto debe expresar el negocio que resuelve, no las tecnologías que utiliza. Los paquetes principales deben ser los contextos de negocio (ej. `identidades`, `logistica`).
- **Vertical Slicing**: Agrupar los elementos necesarios para desarrollar una capacidad o funcionalidad completa (desde la API hasta la base de datos) dentro de un mismo paquete (ej. `gestionusuarios`).

## 2. Lógica de Diseño de Entidades (Domain Modeling)

Antes de programar la base de datos o los controladores, el arquitecto exige diseñar rigurosamente el modelo en la capa de `domain`. Las entidades NO deben ser simples estructuras de datos (anémicas), sino que deben contener el comportamiento del negocio:

1. **Propósito de la Entidad**: Definir claramente qué representa en el sistema (ej. *El Usuario representa a una persona capaz de acceder y operar el sistema*).
2. **Ciclo de Vida (Estados)**: Identificar todos los estados por los que transita a lo largo de su existencia. Debe usarse un `Enum`. Por ejemplo:
   - `PENDIENTE_ACTIVACION`, `ACTIVO`, `INACTIVO`, `SUSPENDIDO`, `CADUCADO`, `DADO_DE_BAJA`.
3. **Comportamiento (Métodos de Dominio)**: Los cambios de estado o datos deben hacerse mediante métodos explícitos del negocio, no con simples `setters`:
   - *Relacionados al ciclo de vida*: `caducar()`, `darDeBaja()`, `renovarVigencia()`, `reactivar()`, `suspender()`, `desactivar()`, `cambiarOrigen()`, `cambiarTipoCuenta()`.
   - *Relacionados a los datos*: `cambiarNombre()`, `cambiarCorreo()`.
   - *Relacionados al acceso*: Lógica de cómo se identifica y autentifica (si aplica al modelo).
4. **Reglas de Negocio (Invariantes)**: La entidad protege su propia consistencia. Debe aplicar:
   - Reglas de creación (ej. "Al nacer, siempre está PENDIENTE_ACTIVACION").
   - Reglas particulares de estado (ej. "Un usuario DADO_DE_BAJA no puede cambiar su correo").
   - Reglas de autoría/auditoría (quién lo hizo, cuándo).

## 3. Estructura de Capas (Dentro de un Vertical Slice)

Cada funcionalidad debe dividirse en las siguientes capas, respetando la regla: **El dominio permanece aislado**.

### Domain (`domain`)
El corazón del negocio donde se aplica la *Lógica de Diseño de Entidades*. Cero dependencias de Spring.
- **`model`**: Entidades ricas con estado y comportamiento (como se definió arriba).
- **`exception`**: Excepciones de negocio personalizadas.
- **`service`**: Servicios de dominio para lógica que involucra múltiples entidades.

### Application (`application`)
Orquesta los casos de uso.
- **`port.in`**: Interfaces que definen los casos de uso.
- **`port.out`**: Interfaces para lo que el sistema necesita de afuera (ej. repositorios).
- **`command`**: DTOs/Records con los datos de entrada.
- **`service`**: Implementaciones de los `port.in` (Orquestan a la entidad para que ejecute su comportamiento).

### Infraestructura (`infraestructura`)
Detalles técnicos y frameworks.
- **`adapter.in.rest`**: Controladores HTTP.
- **`adapter.out.persistence`**: Repositorios de base de datos.
- **`config`**: Configuración de Spring.

## Instrucciones para el Agente (LLM)
Cuando el usuario te pida crear una funcionalidad:
1. **Primero analiza la Entidad**: Diseña su ciclo de vida (enum), su comportamiento (métodos) y sus reglas.
2. Crea el **Command** y el **Port In** en `application`.
3. Implementa el **Service** de aplicación que llamará a los métodos de la entidad.
4. Define el **Port Out** y conéctalo en `infraestructura/adapter.out.persistence`.
5. Expón la API en `infraestructura/adapter.in.rest`.
