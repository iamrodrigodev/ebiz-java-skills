---
name: usecases
description: Guia de orquestacion para Casos de Uso (Application Services).
---

# Guia de Casos de Uso (Application Services)

**Objetivo:** La capa Application representa la orquestacion de un caso de uso. Su responsabilidad no es implementar reglas de negocio, sino coordinar la colaboracion entre el dominio y los servicios externos necesarios para completar la operacion. Application traduce, Domain razona.

## Reglas de Implementacion y Arquitectura

1. **Commands como Entrada:** La aplicacion recibe intenciones representadas en objetos inmutables (`Command`), no entidades de dominio ni DTOs HTTP.
2. **Resultados Estables:** Se debe evitar devolver la Entidad de Dominio completa hacia afuera, ya que acopla los consumidores al crecimiento interno de la entidad. Retornar solo lo que el caso de uso necesite (`UsuarioId` o un `*Result`).
3. **Validaciones Previas:** Application puede consultar puertos para validaciones externas (ej. "el correo ya existe en BD") antes de ceder el control al dominio.
4. **Instanciacion sin Spring:** Las clases se nombran con el sufijo `*Service`, pero NO utilizan la anotacion `@Service`. Se registran manualmente en la configuracion.

## Lo que SI debes hacer (Buenas Practicas)

```java
import java.util.Objects;

// El servicio no lleva la anotacion @Service
public final class CrearUsuarioService implements CrearUsuarioUseCase {
    
    private final ConsultarUsuarioPort consultarUsuarioPort;
    private final GuardarUsuarioPort guardarUsuarioPort;

    public CrearUsuarioService(ConsultarUsuarioPort consultarUsuarioPort, GuardarUsuarioPort guardarUsuarioPort) {
        this.consultarUsuarioPort = Objects.requireNonNull(consultarUsuarioPort, "Puerto no puede ser nulo");
        this.guardarUsuarioPort = Objects.requireNonNull(guardarUsuarioPort, "Puerto no puede ser nulo");
    }

    @Override
    public UsuarioId crearUsuario(CrearUsuarioCommand command) {
        if (command == null) throw new IllegalArgumentException("El comando no puede ser nulo");

        // 1. Validar reglas externas
        if (consultarUsuarioPort.existeUsuarioConCorreo(new CorreoElectronico(command.correo()))) {
            throw new CorreoElectronicoYaRegistradoException(command.correo());
        }

        // 2. Coordinar creacion con el Dominio
        Usuario usuario = Usuario.crear(
            new PersonaId(command.personaId()),
            command.nombre(),
            new CorreoElectronico(command.correo())
        );

        // 3. Persistir y retornar solo el ID
        return guardarUsuarioPort.guardar(usuario);
    }
}
```

## Lo que NO debes hacer (Anti-patrones)

- Implementar `if(usuario.edad < 18)` dentro del Application Service. El calculo y evaluacion de la edad pertenece al dominio.
- Utilizar `@Autowired` o `@Service` en la capa de aplicacion.
- Retornar la clase `Usuario` al finalizar el metodo, exponiendo toda su data al controlador HTTP.

## Instrucciones Especificas para Agentes IA

- Siempre que crees un caso de uso, asegurate de recibir un objeto `Command` inmutable.
- Nunca apliques reglas de validacion intrinsecas (ej. formato del correo) en el servicio; esa es tarea del Value Object en el Dominio.
- Utiliza la convencion de inyeccion por constructor con verificacion `Objects.requireNonNull`.
