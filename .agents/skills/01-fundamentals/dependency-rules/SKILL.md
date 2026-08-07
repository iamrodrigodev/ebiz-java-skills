---
name: dependency-rules
description: Guia formal de las reglas de dependencia e imports entre las capas.
---

# Guia de Reglas de Dependencia

**Objetivo:** Proteger los limites arquitectonicos definiendo estrictamente que paquetes e importaciones estan permitidos en cada capa de la Arquitectura Hexagonal.

## Reglas de Implementacion y Arquitectura

1. **Regla de Oro:** Las capas internas nunca deben depender de las capas externas.
2. **Capa Domain:** Totalmente aislada y pura. Solo depende de las bibliotecas estandar de Java (`java.lang`, `java.util`, `java.time`). Se permite Jakarta Validation solo si el equipo lo aprobo explícitamente para Value Objects, pero ninguna otra libreria externa.
3. **Capa Application:** Orquesta el negocio coordinando Domain e Infraestructura. Conoce al Domain, pero no conoce a Infrastructure. No se debe importar `java.sql` o librerias de frameworks web.
4. **Capa Infrastructure:** Conoce a Application y Domain. Aqui residen las dependencias concretas (Spring Web, Spring Data JPA, Drivers de SQL Server).

## Lo que SI debes hacer (Buenas Practicas)

- Validar mediante herramientas de analisis estatico o revisiones de PR que los imports cumplan estas reglas.
- Definir contratos puros (Puertos de Entrada y Salida) en Application para que Infrastructure los implemente y consuma.

## Lo que NO debes hacer (Anti-patrones)

```java
// EN LA CAPA APPLICATION (Ej: application/service/UsuarioService.java)
import org.springframework.web.bind.annotation.RestController; // ERROR: Framework HTTP
import pe.com.empresa.infrastructure.adapter.UsuarioJpaRepository; // ERROR: Persistencia directa
import jakarta.servlet.http.HttpServletRequest; // ERROR: Detalles de protocolo
```

```java
// EN LA CAPA DOMAIN (Ej: domain/model/Usuario.java)
import jakarta.persistence.Entity; // ERROR: Persistencia
import lombok.Data; // ERROR: Entidad anemica
import pe.com.empresa.application.command.CrearUsuarioCommand; // ERROR: Dependencia hacia afuera
```

## Instrucciones Especificas para Agentes IA

- Nunca generes codigo en la capa de dominio (`domain/model`) que importe dependencias distintas a las nativas de Java.
- Nunca generes un Caso de Uso (`application/service`) que utilice anotaciones `@Autowired`, `@Service` (a menos que este configurado explicitamente) o que importe implementaciones tecnicas.
- Si el usuario requiere mapear informacion proveniente del exterior, asegurate de que Application o Infrastructure realicen la traduccion hacia el lenguaje del Dominio.
