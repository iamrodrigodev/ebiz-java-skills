---
name: politicas-e-interfaces
description: "Definición e implementación de Servicios de Dominio Puros."
---

# Políticas de Dominio (Domain Services)

Existen lógicas y reglas de negocio complejas que no "pertenecen" naturalmente a una única entidad, sino que orquestan interacciones entre varias o dependen de cálculos externos que aún son puramente negocio. En DDD se llaman Servicios de Dominio o Políticas.

**Flujo Obligatorio:**
1. **[OBLIGATORIO] Definir Interfaces:** Primero se declara la interfaz de la política.
   - **Ejemplo en el proyecto:** Interfaz `PoliticaVigenciaUsuario`.
2. **Implementación Pura:** Luego se implementa la lógica concreta.
   - **Ejemplo en el proyecto:** Clase `PoliticaVigenciaUsuarioEstandar`.

**Regla Estricta:**
- **[PROHIBIDO] Fugas de Infraestructura:** Una política de dominio puro NO DEBE depender de puertos de acceso a datos (ej. Port-Out a BD). Si necesita datos para calcular la vigencia, la capa de Application (el Handler) debe obtener esos datos usando el Port-Out y pasárselos como argumentos a la Política.
