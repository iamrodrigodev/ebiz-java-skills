---
name: estructura-de-paquetes-slicing
description: Reglas de Vertical Slicing.
---

# Vertical Slicing
Cada capability se agrupa por intención de negocio: `crearusuario`, `modificarusuario`, `consultarusuarios`, etc. No se agrupan por capas tecnológicas (ej. prohibido usar carpetas genéricas como `services` o `controllers` que mezclen distintos flujos).
