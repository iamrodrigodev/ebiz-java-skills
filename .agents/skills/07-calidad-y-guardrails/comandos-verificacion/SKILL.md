---
name: comandos-verificacion
description: Quality Gate local.
---

# Quality Gate Local
Antes de considerar terminada una modificación estructural, ejecuta:
`mvn clean verify`
Verifica arranque independiente:
`java -jar .\logistica-read\target\logistica-read-0.0.1-SNAPSHOT.jar` (puerto 8081)
`java -jar .\logistica-write\target\logistica-write-0.0.1-SNAPSHOT.jar` (puerto 8082)
