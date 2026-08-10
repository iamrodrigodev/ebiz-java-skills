---
name: screaming-architecture
description: "Nombres de paquetes, clases y variables que revelan el dominio del negocio."
---

# Screaming Architecture (Arquitectura que Grita su Intención)

**Definición:**
Cuando un desarrollador abre el proyecto, lo primero que debe ver son los conceptos del negocio, no las herramientas tecnológicas. El código debe "gritar" de qué se trata el sistema.

**Reglas Inquebrantables:**
1. **[OBLIGATORIO] Nomenclatura Orientada a Negocio:** Los nombres de clases y packages deben revelar capacidades del negocio.
2. **Ejemplos Correctos (Golden Path):**
   - Paquetes: `gestionusuarios`, `auditoria`.
   - Clases: `AprobarPedidoCommand`, `PoliticaVigenciaUsuario`.
3. **[PROHIBIDO] Nomenclatura Tecnológica:** 
   - No usar nombres como `UsuarioManager`, `UsuarioHelper`, `Utils`.
   - No agrupar por tecnología en las raíces del negocio (ej. evitar un paquete `hibernate-entities` global).
