---
name: estructura-de-paquetes-slicing
description: "Reglas estrictas de Vertical Slicing e Intención de Negocio."
---

# Vertical Slicing (Corte por Funcionalidad)

**Definición:**
La arquitectura no se divide en capas tecnológicas planas donde todo se mezcla. Se divide en "Capabilities" o Casos de Uso. Cada capability contiene todo lo necesario para que ese flujo funcione de punta a punta.

**Reglas Inquebrantables:**
1. **[OBLIGATORIO] Agrupar por intención:** Cada capability se agrupa por su intención de negocio. 
   - Ejemplos correctos: `crearusuario`, `modificarusuario`, `consultarusuarios`, `dardebajausuario`.
2. **[PROHIBIDO] Capas horizontales genéricas:** No puedes tener un paquete llamado `services/` que adentro tenga 50 servicios distintos, ni un paquete `controllers/` gigante. 
3. **Aislamiento:** El código de `crearusuario` no debe depender del código de `modificarusuario` salvo que interactúen mediante puertos o compartan el dominio puro.

**Evidencia en el Proyecto el proyecto:**
Revisa cómo la capa de `[proyecto]-application-write` tiene sus paquetes separados por cada intención (Command) y no por tipo de clase. Cada carpeta contiene su propio Command, UseCase, Port-Out y Handler específicos.
