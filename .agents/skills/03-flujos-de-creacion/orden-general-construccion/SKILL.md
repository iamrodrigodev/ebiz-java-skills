---
name: orden-general-construccion
description: "Orden transversal de creación de código en un proyecto (pasos 1 al 9)."
---

# Orden General de Creación (Flujo Completo)

**El orden no es arbitrario.** 
Se construye siempre desde las reglas estables del negocio (Dominio) hacia los detalles tecnológicos volátiles (Infraestructura, REST). Así, cada paso compila y puede probarse con tests unitarios antes de avanzar.

**La Secuencia Exacta (Fases 1 y 2 - Dominio):**
1. **Fundamentos transversales de dominio:**
   - Crear Value Objects de identidad (`ActorAuditoriaId`, `PersonaId`, `UsuarioId`).
   - Crear `EntidadAuditada` si se comparte auditoría.
   - Crear Value Objects con invariantes (`CorreoElectronico`, `PeriodoVigencia`).
   - Crear enums del dominio (`EstadoCuenta`, `OrigenUsuario`, `TipoCuenta`).
2. **Políticas y agregado:**
   - Definir interfaces de políticas de dominio (`PoliticaVigenciaUsuario`).
   - Implementar la política concreta (`PoliticaVigenciaUsuarioEstandar`).
   - Crear el agregado principal (ej. `Usuario`) con factory methods estáticos y comportamiento rico.
   - **[OBLIGATORIO]** Cerrar esta fase con tests unitarios del dominio (sin Spring).
3. Avanzar al paso 3 (Creación de Capability WRITE) o Paso 4 (Creación de Capability READ).
