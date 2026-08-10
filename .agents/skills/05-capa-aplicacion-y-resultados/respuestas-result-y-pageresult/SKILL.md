---
name: respuestas-result-y-pageresult
description: Contratos de salida Application.
---

# Respuestas de Application (Result / PageResult)
- Crear `Result` sólo si el caso de uso (WRITE/READ) retorna datos.
- Estos objetos son independientes de JPA y REST. No llevan anotaciones Jackson ni Hibernate.
