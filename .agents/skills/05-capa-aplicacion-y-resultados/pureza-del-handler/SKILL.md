---
name: pureza-del-handler
description: "La prohibición estricta de usar anotaciones framework en Application."
---

# Pureza del Handler (La Regla de Oro)

El `Handler` (el implementador del UseCase) contiene el valor de cómo orquestar la funcionalidad. Es el código que más va a cambiar. Debe ser puro Java.

**Regla de Sangre:**
- **[PROHIBIDO EN ABSOLUTO]** Colocar anotaciones de inyección de Spring Boot como `@Service`, `@Component`, `@Autowired` sobre los Handlers de la capa de Application.
- **[PROHIBIDO]** Colocar la anotación `@Transactional` de Spring en el Handler (se coloca en el Decorador en Infraestructura).

**¿Cómo se inyectan las dependencias si no uso @Autowired?**
Mediante el **Constructor**. El Handler debe declarar un constructor público que reciba las interfaces (Port-Outs) que necesita, o puedes generar el constructor usando `@RequiredArgsConstructor` de Lombok, siempre y cuando no agregues `@Component` a nivel de clase. Luego, en la capa de infraestructura, usarás un `@Bean` de Configuración explícito para instanciar el Handler y pasarle las implementaciones de los adaptadores al constructor.
