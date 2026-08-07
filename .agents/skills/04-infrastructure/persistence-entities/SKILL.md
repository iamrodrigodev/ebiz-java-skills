---
name: persistence-entities
description: Estandar para el diseno y creacion de Entidades JPA (Base de Datos).
---

# Guia de Entidades JPA (Persistence Entities)

**Objetivo:** Definir la representacion tecnica utilizada por JPA para almacenar y recuperar informacion en la base de datos (SQL Server), garantizando que estos detalles fisicos no contaminen el Modelo de Dominio.

## Reglas de Implementacion y Arquitectura

1. La Entidad JPA debe representar columnas, claves y restricciones fisicas.
2. NO debe reutilizarse la Entidad del Dominio puro como entidad JPA. La separacion asegura que el dominio permanezca libre de anotaciones de Hibernate.
3. El constructor vacio exigido por JPA debe ser declarado como `protected` para evitar su libre instanciacion por desarrolladores de forma incompleta.
4. Solo se deben usar tipos de datos nativos compatibles con SQL (`Long`, `String`, `Character`), jamas inyectar un Value Object del Dominio directamente en los atributos JPA.

## Lo que SI debes hacer (Buenas Practicas)

```java
import jakarta.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "usuario", schema = "dbo", uniqueConstraints = { 
    @UniqueConstraint(name = "UQ_Usuario_Correo", columnNames = "Correo") 
})
public class UsuarioJpaEntity {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "UsuarioId", nullable = false)
    private Long usuarioId;

    @Column(name = "OrigenCodigo", nullable = false, length = 1)
    private Character origenCodigo; // Usa primitivos/nativos, NO OrigenUsuario (enum)
    
    @Column(name = "FechaModificacion")
    private LocalDateTime fechaModificacion;

    protected UsuarioJpaEntity() {
        // Constructor requerido por JPA
    }

    public UsuarioJpaEntity(Long usuarioId, Character origenCodigo, LocalDateTime fechaModificacion) {
        this.usuarioId = usuarioId;
        this.origenCodigo = origenCodigo;
        this.fechaModificacion = fechaModificacion;
    }

    // Getters estrictamente necesarios para el mapeo
    public Long usuarioId() { return usuarioId; }
    public Character origenCodigo() { return origenCodigo; }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Declarar `public UsuarioJpaEntity() {}` y anadir docenas de constructores sin contexto.
- Aplicar logica de negocio, condicionales complejos o instanciacion de dependencias en esta clase.

## Instrucciones Especificas para Agentes IA

- Nunca crees `Setters` publicos en las entidades JPA. La inyeccion de valores ocurre solo a traves de constructores llenos generados para ser consumidos por el Mapper.
- Para nombrar los metodos de acceso (getters), usa el formato de funcion de registro (ej. `nombre()` en lugar de `getNombre()`) si el equipo sigue ese patron de inmutabilidad (como se aprecia en el manual).
