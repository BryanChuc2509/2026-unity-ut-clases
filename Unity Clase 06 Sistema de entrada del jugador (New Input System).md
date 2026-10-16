
> **Versión utilizada:** Unity 6.6 — `6000.6.3f1`  
> **Materia:** Desarrollo de Videojuegos  
> **Materia relacionada:** Gestión en el Desarrollo de Software
---

## 1. Objetivo de la clase

En las clases anteriores aprendimos a mover objetos, aplicar fuerzas, cambiar colores y detectar colisiones. Hasta ahora, para leer el teclado utilizamos instrucciones como `Input.GetKey()` e `Input.GetAxisRaw()`.

En esta clase aprenderemos a utilizar el **New Input System de Unity**, un sistema que permite separar las acciones del jugador de las teclas o dispositivos que las activan.

Al finalizar, serás capaz de:

- Configurar el paquete Input System.
    
- Crear un archivo de acciones de entrada.
    
- Diferenciar `Action Map`, `Action`, `Binding` y `Control`.
    
- Configurar controles con WASD y las flechas del teclado.
    
- Utilizar `PlayerInput` para conectar las acciones con un script.
    
- Controlar el movimiento mediante el nuevo sistema.
    
- Ejecutar acciones como saltar y cambiar de color.
    
- Integrar las entradas con `Rigidbody2D`, fuerzas y colisiones.
    
- Migrar un controlador existente sin perder sus mecánicas.
    

## 2. ¿Por qué necesitamos un nuevo sistema de entrada?

En las primeras clases utilizamos código como este:

```
if (Input.GetKey(KeyCode.Space))
{
    // Ejecutar una acción
}
```

El código pregunta directamente si se está presionando una tecla específica.

Este enfoque funciona para ejercicios sencillos, pero puede dificultar la adaptación de los controles cuando el proyecto crece.

Por ejemplo, imagina que un jugador puede moverse con WASD, con las flechas del teclado o con un control de videojuegos. Si escribimos directamente cada tecla en el script, tendremos que modificar el código para agregar o cambiar controles.

El New Input System nos permite crear una acción llamada `Move` y asignarle varios controles desde el editor. El script consulta la acción, no necesita conocer cada tecla por separado.

### 2.1. Conceptos fundamentales

|Concepto|Significado|Ejemplo|
|---|---|---|
|Input System|Sistema que administra las entradas del jugador.|Leer teclado o gamepad.|
|Action Map|Grupo de acciones relacionadas.|`Player`.|
|Action|Acción que el jugador puede realizar.|`Move`, `Jump`.|
|Binding|Relación entre una acción y un control.|Espacio activa `Jump`.|
|Control|Elemento físico que genera una entrada.|Tecla W, flecha izquierda o botón de un gamepad.|
|PlayerInput|Componente que conecta las acciones con el comportamiento del jugador.|Recibir `Move` y ejecutar el movimiento.|

Una acción representa **lo que quiere hacer el jugador**, mientras que un binding establece **cómo puede solicitarlo**.

Por ejemplo, la acción `Jump` puede estar asociada a la barra espaciadora y, posteriormente, al botón de un gamepad.

### 2.2. Tipos de acciones

Durante la práctica utilizaremos principalmente dos tipos.

**Value**

Permite leer valores que pueden cambiar, como una dirección de movimiento representada mediante un `Vector2`.

**Button**

Representa una acción de tipo botón, como saltar, disparar o cambiar de color.

El tipo de acción determina cómo se interpreta la entrada, pero no decide por sí solo qué hará el personaje. Eso lo programaremos nosotros.

---

# Ejercicio 1 — Configurar las acciones de entrada

**Objetivo:** instalar o verificar el New Input System, crear un archivo de acciones y configurar los controles del jugador.

En este primer ejercicio no construiremos todavía un sistema complejo de movimiento. Primero comprobaremos que las entradas están correctamente configuradas.

## Paso 1. Verificar el paquete

1. Abre tu proyecto de Unity.
    
2. Entra en `Window > Package Management > Package Manager` o abre `Window > Package Manager`, según cómo aparezca en tu versión.
    
3. Selecciona `Unity Registry`.
    
4. Busca `Input System`.
    
5. Si no está instalado, selecciona `Install`.
    
6. Si ya está instalado, verifica que el paquete se encuentre disponible.
    

Unity 6.6 dispone de una versión compatible del paquete Input System. No necesitas instalar una versión manualmente si el administrador ya ofrece una versión compatible con tu editor.

## Paso 2. Configurar el sistema de entrada

1. Abre `Edit > Project Settings > Player`.
    
2. Busca la configuración `Active Input Handling`, dentro de las opciones de configuración del jugador.
    
3. Selecciona `Input System Package (New)` si ya no necesitas el sistema clásico en las escenas que estás ejecutando.
    
4. Si todavía necesitas ejecutar escenas antiguas que utilizan `Input.GetKey()` o `Input.GetAxisRaw()`, puedes seleccionar temporalmente `Both`.
    
5. Si Unity solicita reiniciar el editor, acepta y espera a que el proyecto vuelva a cargar.
    

**Importante:** cambiar esta configuración puede afectar los scripts anteriores. Conserva el código de las clases previas y comprueba qué sistema utiliza cada escena.

## Paso 3. Crear el archivo Input Actions

1. Abre la carpeta `Settings` del proyecto.
    
2. Haz clic derecho en un espacio vacío de la ventana Project.
    
3. Selecciona `Create > Input Actions`.
    
4. Asigna el nombre `PlayerControls`.
    
5. Abre el archivo creado.
    

El editor de Input Actions permite definir mapas, acciones y bindings sin escribir cada tecla directamente en el código.

## Paso 4. Crear el mapa de acciones

Dentro del editor de `PlayerControls`, crea un Action Map llamado:

`Player`

Dentro de ese mapa crea las siguientes acciones:

|Nombre|Action Type|Control Type|
|---|---|---|
|`Move`|Value|Vector2|
|`Jump`|Button|Button|
|`ChangeColor`|Button|Button|

Los nombres deben escribirse exactamente como aparecen en esta tabla, porque los utilizaremos en el componente `PlayerInput` y en los scripts.

## Paso 5. Configurar la acción Move

1. Selecciona la acción `Move`.
    
2. Verifica que su tipo sea `Value`.
    
3. Configura el tipo de control como `Vector2`.
    
4. Agrega un binding de tipo `2D Vector Composite`.
    
5. Configura sus direcciones:
    

|Dirección|Tecla|
|---|---|
|Up|W|
|Down|S|
|Left|A|
|Right|D|

6. Agrega un segundo `2D Vector Composite`.
    
7. Configura las mismas direcciones con las flechas del teclado.
    

Ahora ambas distribuciones de teclas representan la misma acción `Move`.

Cuando se presiona W, la acción produce una dirección hacia arriba. Cuando se presiona A, produce una dirección hacia la izquierda. Si no se presiona ninguna dirección, normalmente el vector será cero.

## Paso 6. Configurar Jump y ChangeColor

Para `Jump`:

1. Selecciona la acción.
    
2. Agrega un binding.
    
3. Asigna la barra espaciadora.
    

Para `ChangeColor`:

1. Selecciona la acción.
    
2. Agrega un binding.
    
3. Asigna la tecla C.
    

## Paso 7. Guardar las acciones

Guarda los cambios desde el editor de Input Actions. Si aparece una opción para generar un archivo de clase C#, no es necesaria para esta práctica porque utilizaremos el componente `PlayerInput` y sus notificaciones.

Cierra el editor cuando hayas terminado.

### Preguntas de análisis

1. ¿Por qué creamos una acción llamada `Move` en lugar de escribir cada tecla en el script?
    
2. ¿Qué diferencia existe entre una acción y un binding?
    
3. ¿Por qué `Move` utiliza `Vector2`?
    
4. ¿Qué ventaja tiene asignar WASD y las flechas a una misma acción?
    
5. Si quisiéramos agregar un gamepad, ¿qué parte de la configuración podríamos ampliar?
    

---

# Ejercicio 2 — Mover un personaje con el New Input System

**Objetivo:** construir una escena donde el personaje se mueva utilizando la acción `Move`.

En este ejercicio utilizaremos `Transform.Translate()` para mantener la mecánica sencilla. El objetivo es comprobar que podemos cambiar la forma de recibir las entradas sin cambiar el concepto de movimiento que aprendimos anteriormente.

## Paso 1. Crear la escena

1. Crea una escena nueva.
    
2. Guárdala como `Escena01_InputMovement`.
    
3. Crea un objeto `2D Object > Sprites > Square`.
    
4. Renómbralo como `Jugador`.
    
5. Configura su posición inicial:
    
    - X: `0`
        
    - Y: `0`
        
    - Z: `0`
        
6. Configura su escala:
    
    - X: `1`
        
    - Y: `1`
        
    - Z: `1`
        
7. Cambia el color del objeto para distinguirlo del fondo.
    

No es necesario agregar un `Rigidbody2D` en este ejercicio porque utilizaremos movimiento directo mediante `Transform`.

## Paso 2. Crear el script de movimiento

Dentro de `Scripts`, crea `MovimientoInput.cs`.

```
using UnityEngine;
using UnityEngine.InputSystem;

public class MovimientoInput : MonoBehaviour
{
    public float velocidad = 5f;

    private Vector2 movimiento;

    public void OnMove(InputValue value)
    {
        movimiento = value.Get<Vector2>();
    }

    void Update()
    {
        Vector3 direccion = new Vector3(
            movimiento.x,
            movimiento.y,
            0f
        );

        transform.Translate(
            direccion * velocidad * Time.deltaTime
        );
    }
}
```

Guarda el script.

## Paso 3. Configurar PlayerInput

1. Selecciona `Jugador`.
    
2. Agrega el componente `PlayerInput`.
    
3. Arrastra `PlayerControls` al campo `Actions`.
    
4. Configura `Default Map` como `Player`.
    
5. En `Behavior`, selecciona `Send Messages`.
    
6. Comprueba que el objeto también tiene el script `MovimientoInput`.
    

El nombre de la acción `Move` se relaciona con el método `OnMove` del script.

Con el comportamiento `Send Messages`, el componente `PlayerInput` busca métodos cuyo nombre corresponda a la acción, utilizando el prefijo `On`.

## Paso 4. Ejecutar la escena

1. Guarda la escena.
    
2. Presiona Play.
    
3. Prueba W, A, S y D.
    
4. Prueba también las flechas del teclado.
    
5. Cambia la velocidad desde el Inspector.
    

El personaje debe responder a ambas distribuciones de teclas sin necesidad de modificar el script.

## Paso 5. Comprender el código

### `using UnityEngine.InputSystem;`

Permite utilizar clases del nuevo sistema de entrada, como `InputValue`.

### `Vector2 movimiento`

Almacena la dirección de movimiento recibida. Un vector permite representar tanto el eje horizontal como el vertical.

### `OnMove(InputValue value)`

Es el método que recibe los cambios de la acción `Move`.

```
movimiento = value.Get<Vector2>();
```

Esta instrucción lee el valor actual de la acción.

Cuando se presionan teclas, el valor cambia. Cuando se sueltan, el sistema comunica el cambio y el vector puede regresar a cero.

### `Update()`

Lee el último valor almacenado y mueve el personaje.

La entrada y el movimiento están separados: `OnMove` actualiza el valor de la dirección, mientras que `Update` lo utiliza para desplazar el objeto.

### `Time.deltaTime`

Ayuda a que la distancia recorrida sea más consistente entre diferentes tasas de fotogramas.

## Paso 6. Experimentar

Realiza estas modificaciones:

1. Cambia `velocidad` a `2`, `5` y `10`.
    
2. Cambia la escala del jugador.
    
3. Modifica su posición inicial.
    
4. Prueba WASD y las flechas.
    
5. Intenta presionar dos direcciones a la vez.
    

Observa qué sucede cuando presionas dos direcciones simultáneamente. Un vector diagonal puede tener una magnitud mayor que un vector horizontal o vertical. Investiga cómo podrías normalizar la dirección para controlar ese comportamiento.

### Preguntas de análisis

1. ¿Qué función cumple `OnMove()`?
    
2. ¿Por qué no utilizamos `Input.GetKey()`?
    
3. ¿Qué relación existe entre `PlayerInput` y `MovimientoInput`?
    
4. ¿Qué ocurriría si el nombre de la acción fuera `Movement` y el método siguiera llamándose `OnMove`?
    
5. ¿Por qué podemos cambiar las teclas sin modificar el código?
    

---

# Ejercicio 3 — Fuerzas, impulso y cambio de color

**Objetivo:** utilizar el New Input System para controlar un cuerpo físico y ejecutar acciones independientes.

En este ejercicio recuperaremos conceptos de las clases anteriores. La entrada del jugador controlará una fuerza aplicada al objeto y una acción de botón permitirá impulsarlo.

## Paso 1. Crear la escena

1. Crea una escena nueva.
    
2. Guárdala como `Escena02_InputPhysics`.
    
3. Crea un objeto `2D Object > Sprites > Square`.
    
4. Renómbralo como `JugadorFisico`.
    
5. Configura su posición inicial en `(0, 0, 0)`.
    
6. Configura su escala en `(1, 1, 1)`.
    
7. Agrega `BoxCollider2D`.
    
8. Agrega `Rigidbody2D`.
    
9. Configura `Body Type` como `Dynamic`.
    
10. Configura `Gravity Scale` como `0` para concentrarnos en el movimiento horizontal y vertical controlado por el jugador.
    
11. Comprueba que el objeto tiene `PlayerInput` y que utiliza `PlayerControls`, con `Default Map` en `Player` y `Behavior` en `Send Messages`.
    

## Paso 2. Crear el script de física

Crea `MovimientoFisicoInput.cs`.

```
using UnityEngine;
using UnityEngine.InputSystem;

public class MovimientoFisicoInput : MonoBehaviour
{
    public float fuerza = 8f;
    public float fuerzaImpulso = 4f;

    private Rigidbody2D rb;
    private Vector2 movimiento;

    void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    public void OnMove(InputValue value)
    {
        movimiento = value.Get<Vector2>();
    }

    void FixedUpdate()
    {
        rb.AddForce(movimiento * fuerza);
    }

    public void OnJump(InputValue value)
    {
        if (value.isPressed)
        {
            rb.AddForce(
                Vector2.up * fuerzaImpulso,
                ForceMode2D.Impulse
            );
        }
    }
}
```

Guarda el archivo y asócialo al objeto `JugadorFisico`.

## Paso 3. Probar el movimiento físico

1. Ejecuta la escena.
    
2. Utiliza WASD o las flechas para mover el objeto.
    
3. Observa cómo responde al aplicar fuerza.
    
4. Cambia `fuerza` desde el Inspector.
    
5. Presiona la barra espaciadora para aplicar un impulso hacia arriba.
    

A diferencia del ejercicio anterior, este objeto utiliza `Rigidbody2D`. La fuerza puede acelerar el cuerpo y su movimiento puede continuar por inercia después de soltar las teclas.

## Paso 4. Comprender la diferencia

En el script utilizamos:

```
rb.AddForce(movimiento * fuerza);
```

Esta instrucción aplica fuerza durante las actualizaciones físicas mientras exista una dirección de entrada.

En cambio:

```
rb.AddForce(
    Vector2.up * fuerzaImpulso,
    ForceMode2D.Impulse
);
```

aplica un impulso instantáneo hacia arriba.

Utilizamos `FixedUpdate()` para aplicar la fuerza en el ciclo de actualización física. La lectura de las acciones puede ocurrir mediante callbacks del Input System, mientras que el cuerpo físico responde a las fuerzas dentro de la simulación.

**Importante:** en este ejemplo, el impulso se aplica cuando el sistema notifica que el botón está presionado. Si se necesita garantizar un único impulso por pulsación y evitar que se repita al mantener la tecla, se debe distinguir el inicio de la acción de su cancelación o liberación.

## Paso 5. Agregar el cambio de color

Crea `CambioColorInput.cs`.

```
using UnityEngine;
using UnityEngine.InputSystem;

public class CambioColorInput : MonoBehaviour
{
    private SpriteRenderer spriteRenderer;
    private bool colorAlternativo;

    void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
    }

    public void OnChangeColor(InputValue value)
    {
        if (!value.isPressed)
        {
            return;
        }

        colorAlternativo = !colorAlternativo;

        spriteRenderer.color = colorAlternativo
            ? Color.red
            : Color.white;
    }
}
```

Asocia el script al mismo objeto `JugadorFisico`.

Ejecuta la escena y presiona C. El objeto debe alternar entre blanco y rojo.

El componente `PlayerInput` puede enviar notificaciones a métodos de los componentes del mismo GameObject. Por eso podemos mantener el movimiento y el cambio de color en scripts separados.

## Paso 6. Analizar los componentes

En este ejercicio, cada componente tiene una responsabilidad:

- `PlayerInput`: conecta las acciones con el objeto.
    
- `MovimientoFisicoInput`: interpreta la entrada y controla el cuerpo físico.
    
- `CambioColorInput`: cambia la apariencia del objeto.
    
- `Rigidbody2D`: participa en la simulación física.
    
- `SpriteRenderer`: muestra el sprite y su color.
    

Separar responsabilidades ayuda a mantener el proyecto organizado y facilita modificar una mecánica sin reescribir todo el comportamiento.

### Preguntas de análisis

1. ¿Qué diferencia existe entre el movimiento del ejercicio 2 y el del ejercicio 3?
    
2. ¿Por qué utilizamos `FixedUpdate()` para aplicar fuerzas?
    
3. ¿Qué función cumple `ForceMode2D.Impulse`?
    
4. ¿Por qué el cambio de color no necesita modificar el `Rigidbody2D`?
    
5. ¿Qué ventaja tiene separar el movimiento y el cambio de color en scripts diferentes?
    

---

# Reto final — Migrar la nave de equilibrio

**Objetivo:** recuperar la mecánica de la Clase 5 y sustituir los controles clásicos por el New Input System.

En este reto no basta con hacer que la nave se mueva. Debes mantener las mecánicas anteriores mientras adaptas la forma de recibir las entradas.

## 1. Crear la escena

Crea una escena nueva llamada `Reto_Input_Equilibrio`.

Construye estos objetos:

### Nave

- Crea un cuadrado llamado `Nave`.
    
- Posición inicial: `(0, -1, 0)`.
    
- Escala: `(3, 0.5, 1)`.
    
- Agrega `BoxCollider2D`.
    
- Agrega `Rigidbody2D`.
    
- Configura `Body Type` como `Dynamic`.
    
- Configura `Gravity Scale` como `0`.
    

### Pelota

- Crea un círculo llamado `Pelota`.
    
- Posición inicial aproximada: `(0, 0, 0)`.
    
- Escala: `(0.5, 0.5, 1)`.
    
- Agrega `CircleCollider2D`.
    
- Agrega `Rigidbody2D`.
    
- Configura `Body Type` como `Dynamic`.
    
- Configura `Gravity Scale` como `1`.
    

### Suelo

- Crea un cuadrado llamado `Suelo`.
    
- Posición: `(0, -4, 0)`.
    
- Escala: `(14, 1, 1)`.
    
- Agrega `BoxCollider2D`.
    
- Deja el suelo como objeto estático, sin un `Rigidbody2D` dinámico.
    

Comprueba que la pelota comience sobre la nave y que ambos objetos tengan colliders.

## 2. Crear el controlador de la nave

Crea `NaveInput.cs`.

```
using UnityEngine;
using UnityEngine.InputSystem;

public class NaveInput : MonoBehaviour
{
    public float fuerza = 8f;

    private Rigidbody2D rb;
    private Vector2 movimiento;

    void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    public void OnMove(InputValue value)
    {
        movimiento = value.Get<Vector2>();
    }

    void FixedUpdate()
    {
        rb.AddForce(movimiento * fuerza);
    }
}
```

Asocia el script al objeto `Nave`.

Agrega `PlayerInput` a la nave y configura:

- `Actions`: `PlayerControls`.
    
- `Default Map`: `Player`.
    
- `Behavior`: `Send Messages`.
    

La nave debe moverse con WASD y con las flechas del teclado.

## 3. Controlar la pelota

Crea `PelotaInput.cs`.

```
using UnityEngine;
using UnityEngine.InputSystem;

public class PelotaInput : MonoBehaviour
{
    public float fuerzaImpulso = 4f;

    private Rigidbody2D rb;

    void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    public void OnJump(InputValue value)
    {
        if (!value.isPressed)
        {
            return;
        }

        rb.AddForce(
            Vector2.up * fuerzaImpulso,
            ForceMode2D.Impulse
        );
    }

    void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.name == "Suelo")
        {
            Debug.Log("La pelota cayó al suelo.");
        }
    }
}
```

Asocia el script al objeto `Pelota`.

Agrega `PlayerInput` a la pelota y utiliza el mismo archivo `PlayerControls`, con `Default Map` en `Player` y `Behavior` en `Send Messages`.

Al tener `PlayerInput` en ambos objetos, cada uno recibirá las acciones que tenga configuradas. En un proyecto más grande, convendría diseñar mapas de acciones específicos para evitar que la misma entrada active comportamientos que no corresponden al jugador.

## 4. Integrar el cambio de color

Agrega el script `CambioColorInput` de la práctica anterior al objeto `Nave`.

Asegúrate de que la acción `ChangeColor` esté configurada en `PlayerControls` y asociada a la tecla C.

Ahora la nave debe poder:

- Moverse mediante fuerzas.
    
- Cambiar de color.
    
- Mantener su collider y su cuerpo físico.
    

La pelota debe poder recibir un impulso y detectar cuándo toca el suelo.

## 5. Probar la mecánica

1. Ejecuta la escena.
    
2. Mueve la nave con WASD y las flechas.
    
3. Presiona C para cambiar su color.
    
4. Presiona espacio para impulsar la pelota.
    
5. Intenta mantener la pelota sobre la nave.
    
6. Observa qué sucede al acelerar o cambiar de dirección.
    
7. Ajusta los valores físicos para mejorar el comportamiento.
    

Si la pelota no se mantiene sobre la nave, no cambies todos los valores al mismo tiempo. Prueba primero la fuerza de la nave, después la fuerza del impulso y finalmente la gravedad de la pelota.

### Mejoras obligatorias

1. **Personalizar controles:** cambia al menos una tecla desde Input Actions y comprueba que no necesitas modificar el script.
    
2. **Modificar la física:** encuentra una combinación de valores que haga más fácil controlar la nave.
    
3. **Detectar la caída:** comprueba que la consola muestre el mensaje cuando la pelota toca el suelo.
    
4. **Documentar pruebas:** registra al menos tres configuraciones y explica qué resultado obtuviste.
    
5. **Comparar sistemas:** explica qué ventajas tiene el New Input System frente al sistema clásico utilizado en las primeras clases.
    

---

## 7. Errores frecuentes y cómo investigarlos

### El personaje no se mueve

Comprueba:

- Que `Input System` está instalado.
    
- Que el archivo `PlayerControls` está asignado a `PlayerInput`.
    
- Que `Default Map` es `Player`.
    
- Que el mapa contiene una acción llamada `Move`.
    
- Que el método se llama `OnMove`.
    
- Que `Behavior` está configurado como `Send Messages`.
    
- Que guardaste los cambios del editor de Input Actions.
    

### El personaje se mueve, pero no se detiene al soltar las teclas

Comprueba que `OnMove` almacena el valor recibido, incluyendo el valor cero cuando se liberan las teclas.

Si utilizas `Rigidbody2D.AddForce()`, el cuerpo puede conservar velocidad por inercia. Esto no significa necesariamente que la entrada esté fallando.

### La barra espaciadora no aplica el impulso

Comprueba:

- Que `Jump` es de tipo `Button`.
    
- Que tiene asignada la barra espaciadora.
    
- Que el método se llama `OnJump`.
    
- Que el objeto tiene `PlayerInput`.
    
- Que el objeto tiene un `Rigidbody2D`.
    

### El cambio de color no funciona

Comprueba que `ChangeColor` está configurada como acción `Button`, que la tecla C está asignada y que el objeto tiene `SpriteRenderer`.

### Los scripts anteriores dejan de funcionar

Es posible que la escena utilice el sistema clásico mientras el proyecto está configurado únicamente para el nuevo sistema.

Revisa `Active Input Handling` y determina qué sistema necesita cada escena. No elimines código antiguo sin verificar primero que la migración está completa.

---

## 8. Entrega y control de versiones

Antes de entregar, comprueba lo siguiente:

- El paquete Input System está instalado y configurado.
    
- Existe el archivo `PlayerControls`.
    
- El mapa `Player` contiene `Move`, `Jump` y `ChangeColor`.
    
- WASD y las flechas controlan el movimiento.
    
- El ejercicio 2 funciona con `Transform`.
    
- El ejercicio 3 funciona con `Rigidbody2D`.
    
- La nave de equilibrio utiliza el New Input System.
    
- La pelota puede recibir un impulso.
    
- El cambio de color funciona.
    
- La colisión con el suelo se detecta.
    
- Las pruebas y conclusiones están documentadas.
    
- Los archivos están guardados y subidos a GitHub.
    

Utiliza un mensaje de commit descriptivo, por ejemplo:

`Migra controles al New Input System`

No subas únicamente capturas de pantalla. Incluye las escenas, scripts y configuraciones necesarias para revisar el proyecto.

---

## 9. Reflexión final

Responde con tus propias palabras:

1. ¿Qué diferencia existe entre una acción y un binding?
    
2. ¿Por qué una acción `Move` puede utilizar varias distribuciones de teclas?
    
3. ¿Qué función cumple `PlayerInput`?
    
4. ¿Qué relación existe entre `OnMove` y la acción `Move`?
    
5. ¿Qué diferencias encontraste entre utilizar `Transform` y aplicar fuerzas?
    
6. ¿Qué problema encontraste durante la migración?
    
7. ¿Cómo agregarías un gamepad a este sistema?
    
8. ¿Qué ventajas tendría separar los mapas de acciones de distintos estados del juego?
    

## 10. Documentación oficial

Consulta las siguientes referencias cuando necesites ampliar o verificar lo aprendido:

- [Input System — Unity 6.6](https://docs.unity.com/en-us/engine/6000.6/manual/packages-list/packages-all/pack-safe/com-unity-inputsystem)
    
- [Input Actions — documentación oficial](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.20/manual/Actions.html)
    
- [PlayerInput — documentación oficial](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.20/manual/PlayerInput.html)
    