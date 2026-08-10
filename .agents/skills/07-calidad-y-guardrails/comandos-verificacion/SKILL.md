---
name: comandos-verificacion
description: "El Quality Gate local ejecutable en terminal."
---

# Comandos de Verificación (Quality Gate Local)

Si haces un refactor de código, siempre debes probar que los microservicios sigan funcionando de forma autónoma.

**1. Verificación Completa de Maven (Ejecuta el Enforcer, JUnit, e Integración):**
```bash
mvn clean verify
```

**2. Verificación de Arranque Independiente (Terminales Separadas):**
Levantar READ:
```bash
java -jar ./[proyecto]-read/target/[proyecto]-read-0.0.1-SNAPSHOT.jar
```
Levantar WRITE:
```bash
java -jar ./[proyecto]-write/target/[proyecto]-write-0.0.1-SNAPSHOT.jar
```

**3. Test de Puertos (Comprobar Binding en Red Local):**
```powershell
Test-NetConnection localhost -Port 8081
Test-NetConnection localhost -Port 8082
```
Si ambos puertos están respondiendo, significa que tu CQRS físico sigue intacto.
