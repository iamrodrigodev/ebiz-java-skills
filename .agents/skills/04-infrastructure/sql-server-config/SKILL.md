---
name: sql-server-config
description: Estandar de configuracion, propiedades y migraciones para SQL Server.
---

# Guia de Configuracion de SQL Server

**Objetivo:** Documentar la configuracion exacta requerida para establecer la conexion con Microsoft SQL Server e imponer directivas strictly sobre el uso del dialecto de Hibernate y DDL.

## Reglas de Implementacion y Arquitectura

1. Hibernate NUNCA debe ejecutar sentencias de `update` o `create` a nivel DDL contra SQL Server en un flujo productivo.
2. Todas las tablas, llaves primarias, identificadores incrementales (`IDENTITY`) y llaves unicas se crean a traves de scripts versionados (Flyway/Liquibase) en recursos fisicos (`.sql`).

## Lo que SI debes hacer (Buenas Practicas)

```properties
# ARCHIVO: src/main/resources/application.properties

spring.application.name=logistica

spring.datasource.url=jdbc:sqlserver://localhost:1433;databaseName=LogisticaDB;encrypt=true;trustServerCertificate=true
spring.datasource.username=logistica_app
spring.datasource.password=logistica_app
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver

# Hibernate NUNCA actualiza, solo valida la paridad de columnas
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl

spring.jpa.open-in-view=false
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.show-sql=true
```

```sql
-- ARCHIVO: src/main/resources/db/migration/V001__crear_tablas.sql
CREATE TABLE dbo.usuario (
    UsuarioId BIGINT IDENTITY(1,1) NOT NULL,
    PersonaId BIGINT NULL,
    Nombre NVARCHAR(200) NOT NULL,
    Correo NVARCHAR(320) NOT NULL,
    OrigenCodigo CHAR(1) NOT NULL,
    TipoCuentaCodigo CHAR(1) NOT NULL,
    FechaInicioVigencia DATE NOT NULL,
    EstadoRegistro BIT NOT NULL,
    CONSTRAINT PK_Usuario PRIMARY KEY (UsuarioId),
    CONSTRAINT UQ_Usuario_Correo UNIQUE (Correo)
);
GO
```

## Lo que NO debes hacer (Anti-patrones)

- Confiar en `spring.jpa.hibernate.ddl-auto=update` para que resuelva migraciones estructurales, exponiendo asi el esquema fisico al azar y a bugs de sincronizacion.

## Instrucciones Especificas para Agentes IA

- Siempre que te pidan conectarte a base de datos en este proyecto, asume el uso del Driver de SQL Server y su cadena de conexion especifica, no uses MySQL ni PostgreSQL.
- Cuando generes codigo de base de datos, asegurate de proveer el script SQL tradicional de MS SQL (usando la palabra clave `GO` y sintaxis como `IDENTITY(1,1)`).
