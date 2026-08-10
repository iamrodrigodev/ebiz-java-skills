---
name: spring-boot-wiring
description: Configuración y arranque.
---

# Wiring y Bootstrap (Paso 8)
- Registrar adapters, handlers, decorators, policies y mappers explícitamente en configuraciones estáticas (ej. `GestionUsuariosReadConfig`).
- Mantener `ComponentScan`, `EntityScan` y `EnableJpaRepositories` restringidos al runtime correcto.
- Levantar `LogisticaReadApplication` y `LogisticaWriteApplication` como procesos independientes.
