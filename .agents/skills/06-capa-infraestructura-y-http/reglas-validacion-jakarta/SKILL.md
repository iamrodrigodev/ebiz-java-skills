---
name: reglas-validacion-jakarta
description: "Validaciones de entrada para proteger la integridad de los Commands."
---

# Reglas de Validación Jakarta (Protocolo)

No podemos permitir que datos inválidos, nulos o mal formados crucen desde la web hacia nuestra capa pura de Application o Dominio.

**Mecánica Obligatoria:**
1. **[OBLIGATORIO] Anotaciones en Request DTOs:** Colocar anotaciones del framework de validación (Jakarta Validation API) como `@NotNull`, `@NotBlank`, `@Email`, `@Size`, `@Pattern` sobre las propiedades de los Request DTOs (ej. sobre los atributos de `CrearUsuarioRequest`).
2. **[OBLIGATORIO] Activar en el Controller:** En el método del Controller, el argumento del Request debe ir precedido por la anotación `@Valid` o `@Validated`.
3. **Manejo del Fallo:** Si la validación falla, el `ExceptionHandler` global debe atrapar la excepción (`MethodArgumentNotValidException`) y devolver un HTTP 400 con los detalles de qué campos fallaron.
