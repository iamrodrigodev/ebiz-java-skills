---
name: write-01-intencion-y-puertos
description: "Pasos 1 al 3 de una capability WRITE: Command, UseCase, Port-Out."
---

# Creación de Capability WRITE (Pasos 1-3)

Cuando necesites construir una nueva funcionalidad que altere el estado del sistema (ej. "Aprobar Pedido"), debes seguir este orden y respetar las reglas.

**Paso 1: Command (La Intención)**
- Define los datos exactos que requiere la operación.
- **Regla:** No contiene NINGUNA anotación HTTP o JPA. Es un POJO/Record puro.
- **Ejemplos en el proyecto:** `CrearUsuarioCommand`, `DarDeBajaUsuarioCommand`, `ModificarUsuarioCommand`.

**Paso 2: UseCase (Port-In)**
- Interfaz que expone la operación hacia el exterior. Solo define el método de ejecución.
- **Ejemplos en el proyecto:** `CrearUsuarioUseCase`, `EliminarFisicamenteUsuarioUseCase`.

**Paso 3: Port-Out (Puertos de Salida)**
- Interfaces que declaran las necesidades de acceso a datos externos.
- **[OBLIGATORIO] Declaración Acotada:** Deben definirse estrictamente por lo que el Handler necesita (cargar un dato, guardar, validar, integrar), no clonar el patrón Repository genérico.
- **Ejemplos en el proyecto:** `ConsultarPersonaPort`, `GuardarUsuarioPort`, `ConsultarOtroUsuarioConCorreoPort`.
