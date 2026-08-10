---
name: testing-adapters-mappers
description: "Cómo probar la traducción de datos e interacción con BD."
---

# Testing de Adapters y Mappers

Aquí ya estamos en infraestructura, por lo tanto, la tecnología interviene.

**Reglas de Testing:**
1. **Mappers Complejos:** Si un RestMapper o PersistenceMapper tiene lógica de conversión muy elaborada (como formatear fechas o componer múltiples campos), se escribe un test unitario normal validando entrada vs salida.
2. **Adapters y Repositorios:** Para comprobar que los queries en base de datos de Spring Data (`@Query`, JPQL) funcionan sin errores sintácticos, está PERMITIDO usar "Test Slices" de Spring como `@DataJpaTest`.
   - **Nota:** Estas pruebas levantarán un contexto reducido de Spring y usarán una base de datos en memoria (H2) o Testcontainers, por lo que serán un poco más lentas. Solo aplicarlas a queries complejos, no a los generados automáticamente (`findById`).
