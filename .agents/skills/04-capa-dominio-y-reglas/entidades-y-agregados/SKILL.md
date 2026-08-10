---
name: entidades-y-agregados
description: "Construcción de Agregados Raíz y EntidadAuditada."
---

# Entidades de Dominio y Agregados

Un Agregado es un clúster de entidades y objetos de valor que se tratan como una sola unidad para la persistencia de datos y garantías de transacciones. 

**Reglas de Diseño de Agregados:**
1. **[OBLIGATORIO] Factory Methods Estáticos:** Utiliza métodos de fábrica como punto de entrada de creación para ser más expresivo y aislar la inicialización.
   - *Ejemplo conceptual:* `Usuario.crearNuevo(id, correo)` o `Usuario.reconstituir(id, correo, fechaCreacion)` cuando cargas de la BD.
2. **Auditoría Transversal:**
   - Si múltiples entidades comparten campos de auditoría (ej. `creadoPor`, `creadoEn`, `actualizadoPor`), se DEBE crear una clase base abstracta en el dominio.
   - **Ejemplo en el proyecto:** Existe la clase `EntidadAuditada`. El Agregado `Usuario` hereda de ella.

**Ejemplos de Agregados en el proyecto:**
- El agregado principal es `Usuario`. Es rico en comportamiento, encapsula su estado y aplica las reglas del negocio.
