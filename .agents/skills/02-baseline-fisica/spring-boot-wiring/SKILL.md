---
name: spring-boot-wiring
description: "Configuración estática, inyección manual y arranque aislado."
---

# Wiring y Bootstrap (Encendido de la App)

Al mantener la capa de Application libre de Spring, debemos "conectar" los cables manualmente en la capa de Infraestructura. Esto se llama "Wiring".

**Reglas de Wiring:**
1. **[OBLIGATORIO] Configuración Estática Explicita:** Se deben registrar explícitamente los adapters, handlers, decorators, policies y mappers en clases de configuración de Spring.
   - **Ejemplos Reales:** `GestionUsuariosReadConfig` (para el runtime de lectura) y `GestionUsuariosWriteConfig` (para escritura).
   - En estas clases `@Configuration` creas métodos `@Bean` que instancian el `Handler` pasándole sus puertos (adapters) y luego lo envuelven en su `TransactionalDecorator`.
2. **[PROHIBIDO] Autodiscovery indiscriminado:** No pongas `@Component`, `@Service` o `@Autowired` en las clases de Dominio ni de Application.
3. **Restricción de Escaneo:** Mantener `@ComponentScan`, `@EntityScan` y `@EnableJpaRepositories` apuntando SOLO al paquete que corresponde al runtime correcto.
4. **Bootstrap Aislado:** El proyecto se levanta mediante dos puntos de entrada separados:
   - `[Proyecto]ReadApplication` (8081).
   - `[Proyecto]WriteApplication` (8082).
