---
name: reglas-de-negocio-e-invariantes
description: "Reglas de pureza, prohibición de anemicidad y control de Lombok."
---

# Reglas de Negocio e Invariantes del Dominio

El dominio encapsula todas las reglas fundamentales del negocio que no cambiarán aunque modifiquemos el motor de base de datos o el framework web. 
Las **Invariantes** son reglas matemáticas o lógicas de tu objeto que siempre deben ser ciertas (ej. una cuenta no puede tener saldo negativo).

**Reglas Estrictas sobre la Construcción del Modelo:**
1. **[PROHIBIDO] Modelo Anémico:** Clases de dominio que solo tienen atributos y todos sus getters/setters públicos, relegando la lógica a un "Service". Las entidades deben tener comportamiento propio.
2. **[PROHIBIDO] Setters Públicos Genéricos:** No puedes tener `public void setEstado(EstadoCuenta)`. Esto permite mutaciones incontroladas. La actualización se hace a través de métodos con intención (ej. `public void darDeBaja()`).
3. **[PROHIBIDO] @NoArgsConstructor de Lombok en el Dominio:** Instanciar un objeto vacío con Lombok permite crear estados inválidos donde las reglas no se cumplieron al nacer.
4. **[OBLIGATORIO] Constructores Validados:** Los constructores deben validar el estado de entrada. Si un correo no cumple un Regex, el constructor debe rechazar la creación lanzando una excepción.
