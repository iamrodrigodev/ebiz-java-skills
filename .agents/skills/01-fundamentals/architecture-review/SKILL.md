---
name: architecture-review
description: Guia de revision arquitectonica para auditorias de codigo.
---

# Guia de Revision de Arquitectura

**Objetivo:** Establecer el marco de evaluacion para auditar el cumplimiento estricto de la Arquitectura Hexagonal y los principios de Domain-Driven Design (DDD) durante la revision de codigo, garantizando un acoplamiento nulo del dominio con la infraestructura.

## Reglas de Implementacion y Arquitectura

1. **Aislamiento de la Capa de Dominio:** El dominio representa el conocimiento y reglas del negocio puros. No debe tener conocimiento ni dependencias de tecnologias externas (Spring, JPA, bases de datos).
2. **Dominio Rico:** El modelo de dominio debe expresar acciones y proteger invariantes desde su nacimiento, no solo ser un contenedor de datos (modelo anemico).
3. **Flujo de Dependencias:** Las dependencias siempre fluyen desde el exterior (Infraestructura) hacia el interior (Dominio). El Dominio no depende de nadie, la Aplicacion depende del Dominio, y la Infraestructura depende de la Aplicacion.

## Lo que SI debes hacer (Buenas Practicas)

- Crear validaciones de invariantes dentro de los constructores (marcados como private o protected) del modelo de dominio.
- Importar en la capa de aplicacion unicamente clases de los paquetes `domain` y `application`.
- Verificar que toda la capa de infraestructura solo interactue con el dominio a traves de los puertos de entrada o salida definidos en la aplicacion.

## Lo que NO debes hacer (Anti-patrones)

- Incluir importaciones como `org.springframework.*`, `jakarta.persistence.*`, o `lombok.Data` en los archivos dentro del paquete `domain`.
- Exponer metodos `setX()` publicos en las entidades de dominio que permitan cambiar el estado sin aplicar la logica de validacion del negocio.
- Instanciar clases de infraestructura directamente desde los Casos de Uso (Application Services).

## Instrucciones Especificas para Agentes IA

- Antes de sugerir o aceptar un cambio, debes verificar que el paquete donde estas trabajando tiene permitidas las importaciones de las librerias que vas a usar.
- Si detectas una entidad de dominio con anotaciones de base de datos (@Table, @Entity), debes refactorizar inmediatamente creando una `JpaEntity` separada en la capa de infraestructura.
- Esta estrictamente prohibido utilizar expresiones coloquiales o justificar romper el aislamiento "para hacerlo mas rapido".
