# Evidencias de la unidad 7

# Actividad 1
1. Analisis ejemplos.
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/8c45d6e8-95bb-4e84-bfa7-03effacc7eca" />  


> La imagen hace referencia a la palabra, al utilizar la segunda "O" de la palabra "Moon" para aludir al satelite, haciendola pequeña y posicionandola un poco más arriba que las demás letras.
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/d4dd89e0-b23f-4491-a6fb-701bae0d4311" />


> Al crear la ilusión de tunel con las dos letra "N" de la palabra, sin perder la figura ya que se pueden ver como la entrada y salida de las otras palabras.
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/620ecbf3-5668-4028-8314-b9e9184d53d6" />


> Juega con la figura de la "L", para que parezca un parentesis y se complementa con la "I" donde el punto lo eleva para dar la ilusión de una carita sonriente (hasta guiñando parece)
<img width="500" height="503" alt="image" src="https://github.com/user-attachments/assets/5032d0c9-9b45-4e50-a61b-026ce59cab50" />


> Juega no solo con los colores de las letras (Cierta transparencia o difuminado) sino que tambien con la posición de ellas. Las posiciona para que se perciba una proximidad y lejania, esto tambien lo logra con el color, ya que entre más oscuro se coloca más cercano parece.


2. Ideas propias

> Mouse: donde la "o" es un ratón, hacerla mas ovalada y que la "s" sea su cola
> Montaña: utilizar la "M" como referencia a una montaña, hacerla más gruesa y grande y que las otras letras parezca que la estan escalando


# Actividad 2

1. Muestra el código de los dos (o más) experimentos básicos que replicaste integrando Matter.js y p5.js.
> Codigo 1
``` Sketch.js
const {Engine, Body, Bodies, Composite} = Matter;

let engine;
let boxes = [];
let ground;


function setup() {
  createCanvas(400,400);
  engine = Engine.create();
  
  ground = new Ground (200, 300, 400, 10);


  
}


function draw(){
  background(220); 
  Engine.update(engine);
  
  for(let i=0; i<boxes.length; i++){
      boxes[i].display();
      }
  ground.display();
}

function mousePressed() {
  boxes.push(new Rect(mouseX, mouseY, 20, 20));
}

```

```ground.js
class Ground {
  constructor(x, y, w, h){
    this.w = w;
    this.h = h;
    
    
    this.ground = Bodies.rectangle(x, y, this.w, this.h, { isStatic: true });
    Composite.add(engine.world, this.ground);

  }
  
  display(){
    push();
    rectMode(CENTER);
    let x = this.ground.position.x;
    let y = this.ground.position.y;
    let angle = this.ground.angle;


    translate(x, y);
    rotate(angle);
    rect(0, 0, this.w, this.h);
    pop();
  }
}
```
```rect.js
class Rect {
  constructor(x, y, w, h){
    this.w = w;
    this.h = h;
    
  this.body = Bodies.rectangle(x, y, this.w, this.h);
  Body.setAngularVelocity(this.body, 0.2);  
  Composite.add(engine.world, this.body);
  
  }
  
  display(){
    push();
    rectMode(CENTER);
    let x = this.body.position.x;
    let y = this.body.position.y;
    let angle = this.body.angle;


    translate(x, y);
    rotate(angle);
    rect(0, 0, this.w, this.h);
    pop();
  
    
  }
}
```

> Ejemplo 2. 
``` sketch.js
// Alias para Matter.js (lo mismo que en tu código original)
var Engine = Matter.Engine,
    World = Matter.World, // Usaremos World en lugar de Composite.add
    Bodies = Matter.Bodies;

// Variables globales para la simulación
var engine;
var world;
var boxes = []; // Arreglo para guardar los cuerpos
var ground;     // La base o pared inferior

// La función setup se ejecuta una vez al inicio
function setup() {
    // 1. Configuración del Canvas de p5.js (en lugar de Matter.Render)
    createCanvas(800, 600);
    rectMode(CENTER); // Dibujar rectángulos desde su centro para mayor facilidad

    // 2. Crear el motor de física
    engine = Engine.create();
    world = engine.world;
    
    // Configurar la gravedad (opcional, por defecto ya es 1)
    // world.gravity.y = 1;

    // 3. Crear los cuerpos
    
    // ** Bloques cayendo (con diferente frictionAir) **
    // Bloque 1: Poca fricción de aire (cae más rápido)
    let boxA = Bodies.rectangle(200, 100, 60, 60, { frictionAir: 0.001, render: { fillStyle: 'red' } });
    // Bloque 2: Fricción de aire media
    let boxB = Bodies.rectangle(400, 100, 60, 60, { frictionAir: 0.05, render: { fillStyle: 'green' } });
    // Bloque 3: Alta fricción de aire (cae más lento)
    let boxC = Bodies.rectangle(600, 100, 60, 60, { frictionAir: 0.1, render: { fillStyle: 'blue' } });
    
    boxes.push(boxA, boxB, boxC); // Guardamos los cuerpos en un arreglo para dibujarlos

    // ** Paredes (isStatic: true) **
    // Nota: Matter.js usa el centro para posicionar. (x, y, ancho, alto)
    let wallTop = Bodies.rectangle(400, 0, 800, 50, { isStatic: true, render: { fillStyle: '#333' } });
    let wallBottom = Bodies.rectangle(400, 600, 800, 50, { isStatic: true, render: { fillStyle: '#333' } });
    let wallRight = Bodies.rectangle(800, 300, 50, 600, { isStatic: true, render: { fillStyle: '#333' } });
    let wallLeft = Bodies.rectangle(0, 300, 50, 600, { isStatic: true, render: { fillStyle: '#333' } });

    // 4. Agregar todos los cuerpos al mundo
    World.add(world, [boxA, boxB, boxC, wallTop, wallBottom, wallRight, wallLeft]);
    
    // 5. Configurar el MouseConstraint (para arrastrar cuerpos)
    // p5.js no maneja el canvas como un elemento del DOM de forma nativa para Matter.js,
    // por lo que usamos la función 'getElement' para obtener el canvas DOM.
    var canvasDom = document.getElementById("defaultCanvas0"); // El ID por defecto de p5.js
    var mouse = Matter.Mouse.create(canvasDom);
    var mouseConstraint = Matter.MouseConstraint.create(engine, {
        mouse: mouse,
        constraint: {
            stiffness: 0.2,
            render: {
                visible: false
            }
        }
    });

    World.add(world, mouseConstraint);
    
    // Opcional: Para que p5.js muestre la posición del mouse
    mouse.pixelRatio = pixelDensity();
}


// La función draw se ejecuta continuamente (aproximadamente 60 veces por segundo)
function draw() {
    background(220); // Limpia el fondo

    // 1. Actualizar el motor de física (Esto reemplaza a Matter.Runner.run)
    Engine.update(engine);
    
    // 2. Dibujar todos los cuerpos
    drawBodies(world.bodies);
    
    // Opcional: Mostrar la velocidad como en tu ejemplo original
    // Lo hacemos con texto para los bloques que caen
    textSize(14);
    fill(0);
    textAlign(CENTER);
    text("0.001 (Rápido)", boxes[0].position.x, boxes[0].position.y - 50);
    text("0.05 (Medio)", boxes[1].position.x, boxes[1].position.y - 50);
    text("0.1 (Lento)", boxes[2].position.x, boxes[2].position.y - 50);
    text("Vel. Y: " + boxes[0].velocity.y.toFixed(2), boxes[0].position.x, boxes[0].position.y + 40);
    text("Vel. Y: " + boxes[1].velocity.y.toFixed(2), boxes[1].position.x, boxes[1].position.y + 40);
    text("Vel. Y: " + boxes[2].velocity.y.toFixed(2), boxes[2].position.x, boxes[2].position.y + 40);
}


// Función auxiliar para dibujar los cuerpos
function drawBodies(bodies) {
    for (var i = 0; i < bodies.length; i++) {
        var body = bodies[i];
        
        // No dibujar los cuerpos que solo sirven para la restricción del mouse
        if (body.label === 'Mouse Constraint') continue;

        // Obtener la posición del cuerpo
        var pos = body.position;
        var angle = body.angle;

        push(); // Guarda la configuración de dibujo actual
        translate(pos.x, pos.y); // Mueve el origen al centro del cuerpo
        rotate(angle); // Rota según el ángulo del cuerpo

        // Dibujo
        stroke(0); // Borde negro
        
        // Usar el color del render si está definido, si no, uno por defecto
        if (body.render && body.render.fillStyle) {
            fill(body.render.fillStyle);
        } else {
            fill(120); // Color por defecto gris
        }
        
        // Dibujar el rectángulo (asumimos que todos son rectángulos en este ejemplo)
        // Matter.js usa 'bounds' para el tamaño, pero si es un rectángulo simple
        // podemos usar width/height, o calcularlo desde los vértices.
        // Asumiendo que todos los cuerpos son rectángulos con su ancho y alto
        // (Esto es una simplificación, para formas complejas se necesita iterar los vértices)
        rect(0, 0, body.bounds.max.x - body.bounds.min.x, body.bounds.max.y - body.bounds.min.y);

        pop(); // Restaura la configuración de dibujo
    }
}
```

2. Incluye una **captura de pantalla o ENLACE a un GIF (no olvides, enlace) de cada experimento funcionando.
> Ejemplo 1: the basic (que tambien hace referencia al video de Patt Vira)
<img width="426" height="419" alt="image" src="https://github.com/user-attachments/assets/c0a06eff-3617-4c4f-af8e-9fb6968b8e6c" />

> Ejemplo 2: ejemplo de la biblioteca de matter con ayuda de geminis :D
<img width="808" height="723" alt="image" src="https://github.com/user-attachments/assets/bb97e269-19a3-432a-b1ff-31725adc659b" />

3. Proporciona tu explicación clara y concisa de los conceptos clave (Engine, World, Bodies, Constraint, MouseConstraint).
> Engine: Es el encargado de crear y actualizar las fisicas del mundo.
> World: Es donde se aplican las fuerzas del engine y aquí se crean los objetos que se van a ver afectador por ella. También aquí es donde se agregan o elimian los objetos.
> Bodies: Son los objetos con los que se aplican las fisicas para así interactuar en el mundo
> Constraint: Define como dos objetos van a interactuar entre ellos, puede hacer que sea una unión o una limitación.
> MouseConstraint: Es lo que ayuda al usuario a interactuar con los objetos de la simulación. Es como `constrain` que funciona con el mouse y el cuerpo más cercano.

4. Menciona brevemente cualquier dificultad encontrada al configurar o usar Matter.js inicialmente.
> Inicialmente al seguir el ejemplo con Patt Vita con sus explicaciones se me hizo muy facil colocarlo en p5js y entender que hacer. Cuando ya quise hacer algo parecido que ella se me dificulto. 



