---
name: orden-general-construccion
description: Orden transversal de creación de un proyecto.
---

# Orden General de Creación
El orden no es arbitrario. Se construye desde las reglas estables del negocio hacia los detalles tecnológicos.
1. Fundamentos transversales de dominio (Value Objects, Enums).
2. Políticas y agregado.
3. Contrato de capability WRITE.
4. Contrato de capability READ.
5. Persistencia (JPA Entity, Repository, Adapter).
6. Frontera transaccional (Decorator).
7. REST (DTOs, Mappers, Controllers).
8. Wiring y bootstrap.
9. Pruebas y guardrails.
