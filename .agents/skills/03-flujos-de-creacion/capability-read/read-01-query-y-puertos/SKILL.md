---
name: read-01-query-y-puertos
description: "Pasos 1 al 4 de una capability READ: Query, Result, UseCase, Port-Out."
---

# Creación de Capability READ (Pasos 1-4: Query y Puertos)

Cuando necesites construir una consulta que NO altera el estado del sistema, sigues este flujo de lectura segregado.

**Paso 1: Query (Intención de lectura)**
- Modela los filtros, criterios de búsqueda y parámetros de paginación de la consulta.
- **Ejemplos:** `ConsultarUsuarioPorIdQuery`, `ConsultarUsuariosQuery`.

**Paso 2: Result / PageResult**
- El contrato de salida (datos devueltos) puramente definido por Application.
- **[PROHIBIDO]** Estar acoplado a `@Entity` de JPA o a anotaciones de JSON/Jackson de REST.
- **Ejemplos:** `ConsultarUsuarioPorIdResult`, `ConsultarUsuariosResult`, `ConsultarUsuariosPageResult`.

**Paso 3: UseCase (Port-In)**
- Interfaz que declara la consulta que se expone hacia afuera.
- **Ejemplos:** `ConsultarUsuarioPorIdUseCase`, `ConsultarUsuariosUseCase`.

**Paso 4: Port-Out (Contrato de persistencia)**
- Contrato de acceso a los datos requeridos por esta consulta específica.
- **Ejemplos:** `ConsultarUsuarioPorIdPort`, `ConsultarUsuariosPort`.
