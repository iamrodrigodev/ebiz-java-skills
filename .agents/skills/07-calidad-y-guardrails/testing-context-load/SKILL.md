---
name: testing-context-load
description: "Verificación transversal del arranque del sistema (Wiring)."
---

# Testing de Context Load (Wiring)

Como los beans (`Handler`, `Decorator`, `Adapter`) se instancian manualmente en archivos `@Configuration` (ej. `GestionUsuariosReadConfig`), el error humano de olvidar inyectar un bean es común.

**La Solución:**
- **[OBLIGATORIO] Prueba de Humo:** Cada runtime (READ y WRITE) debe tener al menos un test vacío que use `@SpringBootTest`.
- **Objetivo:** Si la configuración de Wiring tiene un error (falta un `@Bean`, hay un bucle de dependencias), el intento de levantar la aplicación en el test fallará, atrapando el problema durante la construcción local (build time) y no cuando se despliega a producción.
