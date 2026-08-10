---
name: testing-context-load
description: Pruebas de levantamiento de contexto Spring.
---

# Context Load Tests
- Crear pruebas de humo (`@SpringBootTest`) separadas por runtime (READ y WRITE) para asegurar que la inyección de dependencias (`Wiring`) está bien configurada y el servidor puede arrancar.
