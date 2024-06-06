#Documento explicativo de phaser Nave con bola que rebota
## Phaser

Phaser es un framework de código abierto para el desarrollo de juegos en HTML5 y JavaScript. Proporciona una variedad de herramientas y funcionalidades para crear juegos interactivos y experiencias multimedia. Algunas de sus características incluyen gestión de escenas, sistemas de física, animaciones, manipulación de sprites y sonido, entre otros.

## Relación con la Inteligencia Artificial

Phaser puede utilizarse como plataforma para experimentar con técnicas de inteligencia artificial, como el aprendizaje por refuerzo o la optimización mediante algoritmos genéticos. En el contexto del juego, la inteligencia artificial puede utilizarse para controlar el comportamiento de los personajes no jugadores (NPCs), mejorar la jugabilidad y crear experiencias de juego más dinámicas y desafiantes.

## Aprendizaje en Redes Neuronales

Dentro del ámbito del aprendizaje automático, las redes neuronales son un enfoque popular para resolver una variedad de problemas, incluyendo el reconocimiento de patrones, la clasificación de datos y la toma de decisiones. En el contexto de los juegos, las redes neuronales pueden entrenarse para controlar el comportamiento de los personajes del juego, adaptándose y mejorando su rendimiento a medida que juegan.

## Uso en el Código

En el código proporcionado, se utiliza una red neuronal implementada con la biblioteca `Synaptic.js` para controlar el movimiento del jugador en modo automático. La red neuronal recibe información sobre la posición de la bala con respecto al jugador y genera salidas que indican la dirección en la que debe moverse el jugador para evitar la colisión con la bala. Esto muestra cómo Phaser se puede integrar con técnicas de inteligencia artificial para crear comportamientos de juego automatizados y adaptativos.

```python

function create() {
    juego.physics.startSystem(Phaser.Physics.ARCADE)
    juego.physics.arcade.gravity.y = 0
    juego.time.desiredFps = 30

    fondo = juego.add.tileSprite(0, 0, w, h, 'background')
    jugador = juego.add.sprite(w / 2, h / 2, 'nave')
    juego.physics.enable(jugador)
    jugador.body.collideWorldBounds = true

    bala = juego.add.sprite(0, 0, 'pelota')
    juego.physics.enable(bala)
    bala.body.collideWorldBounds = true
    bala.body.bounce.set(1)
    setRandomBalaVelocity()

    pausa_lateral = juego.add.text(w - 100, 20, 'Pausa', {
        font: '20px Arial',
        fill: '#fff',
    })
    pausa_lateral.inputEnabled = true
    pausa_lateral.events.onInputUp.add(pausar, self)
    juego.input.onDown.add(manejarPausa, self)

    cursores = juego.input.keyboard.createCursorKeys()

    Network = new synaptic.Architect.Perceptron(5,10,10,4)
    Network_Entramiento = new synaptic.Trainer(Network)

}

function setRandomBalaVelocity() {
  var speed = 550
  var angle = juego.rnd.angle()
  while(angle < 45 || angle > 75){
    angle = juego.rnd.angle();
  }
  console.log(angle)
  bala.body.velocity.set(Math.cos(45) * speed, Math.sin(60) * speed)
}

function update() {
    fondo.tilePosition.x -= 1

    if (!modo_automatico) {
        manejarMovimientoManual()
    } else {
        if (entrenamiento.length > 0) {
            manejarMovimientoAutomatico()
        } else {
            jugador.body.velocity.x = 0
            jugador.body.velocity.y = 0
        }
    }

    juego.physics.arcade.collide(bala, jugador, colisionar, null, this)
}

function manejarMovimientoManual() {
    var prevX = jugador.body.velocity.x
    var prevY = jugador.body.velocity.y

    jugador.body.velocity.x = 0
    jugador.body.velocity.y = 0

    var moveLeft = cursores.left.isDown ? 1 : 0
    var moveRight = cursores.right.isDown ? 1 : 0
    var moveUp = cursores.up.isDown ? 1 : 0
    var moveDown = cursores.down.isDown ? 1 : 0

    if (moveLeft) {
        jugador.body.velocity.x = -300
    } else if (moveRight) {
        jugador.body.velocity.x = 300
    }

    if (moveUp) {
        jugador.body.velocity.y = -300
    } else if (moveDown) {
        jugador.body.velocity.y = 300
    }

    // Solo registrar si hubo un cambio en el movimiento
    if (jugador.body.velocity.x !== prevX || jugador.body.velocity.y !== prevY) {
        registrarDatosEntrenamiento()
    }

    // Mostrar los datos en la consola
    console.log('-------------DATOS DE CONSOLA---------------')
    console.log('Manual - Movimiento:')
    console.log('Izquierda: ${moveLeft}')
    console.log('Derecha: ${moveRight}')
    console.log('Arriba: ${moveUp}')
    console.log('Abajo: ${moveDown}')
}
```

## Explicación del Código
- Se definen las variables esenciales del juego, como el tamaño del lienzo, los sprites del jugador, el fondo y la bala.
- Se cargan los recursos del juego, como las imágenes del fondo, la nave del jugador y la pelota (bala).
- En la función `create()`, se crea la escena del juego, se configuran las físicas, se añaden los sprites y se inicializa la red neuronal.
- La función `update()` actualiza la lógica del juego en cada fotograma, incluyendo el movimiento del fondo, la detección de colisiones y la gestión del movimiento del jugador.
- Las funciones `manejarMovimientoManual()` y `manejarMovimientoAutomatico()` definen el comportamiento del jugador según el modo de juego seleccionado (manual o automático).
- `registrarDatosEntrenamiento()` registra los datos de entrenamiento si el jugador se está moviendo manualmente y hay movimiento en la bala.
- `colisionar()` se ejecuta cuando la bala colisiona con el jugador, cambiando al modo automático y pausando el juego.
- `pausar()` y `manejarPausa(event)` gestionan la pausa del juego y muestran un menú de pausa para reiniciar el juego o continuar en modo automático.
- `resetGame()` reinicia el juego a su estado inicial cuando se selecciona reiniciar desde el menú de pausa.

## Lineas Importantes 
 `cursores = juego.input.keyboard.createCursorKeys()`: Esta línea crea un objeto `cursores` que contiene referencias a las teclas de flecha del teclado. Este objeto se puede utilizar para verificar si las teclas de flecha están siendo presionadas en un momento dado.

- `Network = new synaptic.Architect.Perceptron(5,10,10,4)`: Esta línea crea una nueva red neuronal utilizando la arquitectura Perceptrón. Los números proporcionados (5, 10, 10, 4) representan el número de neuronas en cada capa oculta de la red neuronal.

- `Network_Entramiento = new synaptic.Trainer(Network)`: Esta línea crea un objeto `Network_Entramiento` que se utiliza para entrenar la red neuronal `Network`. Se utiliza el algoritmo de entrenamiento proporcionado por la biblioteca Synaptic.js para ajustar los pesos de la red neuronal y mejorar su rendimiento en una tarea específica.

## Conclusiones

- **Integración de Phaser y Redes Neuronales**: El uso de Phaser en combinación con una red neuronal permite crear juegos interactivos y adaptativos. Esto muestra cómo se pueden combinar tecnologías de desarrollo de juegos y técnicas avanzadas de inteligencia artificial para mejorar la jugabilidad y crear experiencias más desafiantes.

- **Entrenamiento de Redes Neuronales**: La implementación de una red neuronal utilizando Synaptic.js demuestra la capacidad de entrenar modelos para realizar tareas específicas dentro del juego, como el control automático del jugador. Esto subraya la importancia de tener conjuntos de datos de entrenamiento robustos y bien diseñados para mejorar la precisión y el rendimiento del modelo.

- **Modo Manual vs. Automático**: La capacidad de alternar entre el modo manual y automático proporciona una visión clara de cómo las decisiones humanas y las decisiones automatizadas pueden influir en el comportamiento del juego. Esto puede ser útil para desarrollar sistemas de inteligencia artificial que aprendan de las acciones humanas y mejoren con el tiempo.

- **Importancia de la Interacción del Usuario**: Las funciones de pausa y gestión de entradas resaltan la importancia de la interacción del usuario en el desarrollo de juegos. Proporcionar controles intuitivos y opciones de pausa mejora la experiencia del usuario y permite una mayor flexibilidad en la jugabilidad.

- **Potencial de Experimentación**: Este proyecto sirve como una base sólida para futuras investigaciones y experimentaciones en el campo de la inteligencia artificial aplicada a los juegos. Los desarrolladores pueden expandir y mejorar las funcionalidades del juego, explorando nuevas técnicas y algoritmos para mejorar la interacción y la adaptabilidad de los personajes del juego.
