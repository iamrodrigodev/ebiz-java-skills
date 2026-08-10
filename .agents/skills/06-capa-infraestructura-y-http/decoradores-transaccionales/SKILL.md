---
name: decoradores-transaccionales
description: Patrón Decorador para @Transactional.
---

# Frontera Transaccional
- Crear un `TransactionalDecorator` por UseCase.
- Aplicar `@Transactional(readOnly=true)` a READ y `@Transactional` a WRITE en el decorador.
- El decorador delega al Handler original inyectado por constructor, sin duplicar lógica de negocio.
