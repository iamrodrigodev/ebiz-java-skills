---
name: sql-server-config
description: Guía oficial de configuración para SQL Server, JPA Entity y Flyway.
---

# Guía de Configuración: SQL Server

## 1. Configuración de `application.properties`

```properties
spring.application.name=logistica

spring.datasource.url=jdbc:sqlserver://localhost:1433;databaseName=LogisticaDB;encrypt=true;trustServerCertificate=true
spring.datasource.username=logistica_app
spring.datasource.password=logistica_app
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver

# Hibernate NUNCA debe hacer update en produccion, solo validate
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl

spring.jpa.open-in-view=false
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.show-sql=true
```

## 2. Creación Física con Migraciones SQL
Los scripts de creación deben vivir versionados (ej. `V001__crear_tablas.sql` en `src/main/resources/db/migration`).

```sql
CREATE TABLE dbo.usuario (
    UsuarioId BIGINT IDENTITY(1,1) NOT NULL,
    PersonaId BIGINT NULL,
    Nombre NVARCHAR(200) NOT NULL,
    Correo NVARCHAR(320) NOT NULL,
    OrigenCodigo CHAR(1) NOT NULL,
    TipoCuentaCodigo CHAR(1) NOT NULL,
    EstadoCuentaCodigo INT NOT NULL,
    FechaInicioVigencia DATE NOT NULL,
    FechaFinVigencia DATE NOT NULL,
    EstadoRegistro BIT NOT NULL,
    FechaCreacion DATETIME2(7) NOT NULL,
    ActorCreacionId BIGINT NOT NULL,
    FechaModificacion DATETIME2(7) NULL,
    ActorModificacionId BIGINT NULL,
    CONSTRAINT PK_Usuario PRIMARY KEY (UsuarioId),
    CONSTRAINT UQ_Usuario_Correo UNIQUE (Correo)
);
GO
```

## 3. Mapeo de Entidad JPA (Infrastructure)
La `JpaEntity` usa los tipos simples correspondientes a las columnas (no usa Value Objects) y aplica constructores `protected`.

```java
@Entity
@Table(
    name = "usuario",
    schema = "dbo",
    uniqueConstraints = { @UniqueConstraint(name = "UQ_Usuario_Correo", columnNames = "Correo") }
)
public class UsuarioJpaEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "UsuarioId", nullable = false)
    private Long usuarioId;

    @Column(name = "Nombre", nullable = false, length = 200)
    private String nombre;

    @Column(name = "OrigenCodigo", nullable = false, length = 1)
    private Character origenCodigo;

    protected UsuarioJpaEntity() {} // Constructor requerido por JPA
    
    // Constructores con parámetros y Getters manuales. 
    // PROHIBIDO USAR Setters genéricos públicos si los constructores ya hacen el trabajo.
}
```
