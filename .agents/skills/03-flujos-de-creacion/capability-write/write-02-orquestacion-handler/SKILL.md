---
name: write-02-orquestacion-handler
description: "Paso 4 de una capability WRITE: El Handler de Application."
---

# Creación de Capability WRITE (Paso 4: Handler)

Este es el cerebro del caso de uso. Aquí reside toda la lógica de aplicación.

**Paso 4: El Handler**
- Orquesta el uso de los componentes del Dominio y los puertos definidos.
- **Regla de Pureza Absoluta [PROHIBIDO]:** No puede usar anotaciones de Spring (ni `@Service`, ni `@Autowired`, ni `@Transactional`). Todo se inyecta por constructor estandar.
- **Flujo Típico:**
  1. Recibe el `Command`.
  2. Llama a un Port-Out para obtener estado previo o validar existencia.
  3. Llama a métodos de negocio del Agregado (Dominio puro).
  4. Llama a un Port-Out para persistir los cambios.
  5. Retorna un `Result` o lanza una `Exception` de negocio pura.

**Ejemplos Reales en el proyecto:** 
- `CrearUsuarioHandler`
- `ModificarUsuarioHandler`
- `DarDeBajaUsuarioHandler`
- `EliminarFisicamenteUsuarioHandler`

**Importante:** Después de codificar el Handler (Paso 4), debes escribir los **Tests del Handler (Paso 5)** usando Mocks (ej. Mockito) inyectados por constructor, comprobando toda la lógica SIN levantar el contexto de Spring.
