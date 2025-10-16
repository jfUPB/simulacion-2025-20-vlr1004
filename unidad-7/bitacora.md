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


# Actividad 3
1. Palabra:  
> Montaña 
2. Animación:  
Opción 1
> Cae la M y que parezca pesada, luego aparecen las otras letras
> La M esta estatica pero se va moviendo solo el lado derecho para que luega aparezcan las otras letras y caigan ahi
> La M surja desde abajo y las otras letras tiemblen cuando sube y que se pueda mover las otras letras menos la M 
Opción 2:
> La palabra MOUSE, se agarra la S y se estira para contectarla con la o y se convierte en ratón
Opción 3.
> La palabra BOUNCE, se mueve como un resorte entre más la agarra y luego se suelta para mostrar el rebote
3. Aspectos claves
> En la implementación, cada letra de la palabra se formó como un cuerpo rectangular de Matter.js, utilizando la función `Bodies.rectangle()` para representar su forma física dentro del motor. A cada letra se le asignaron propiedades como **restitution** (para controlar el rebote), **friction** (para simular la resistencia al contacto) y **isStatic** (para mantenerlas quietas hasta que se active la interacción). No se usaron restricciones (*constraints*) porque las letras se comportan como cuerpos independientes que responden libremente a la gravedad y las colisiones. Sin embargo, se aplicó una lógica visual de *squash & stretch* para simular deformaciones elásticas al impactar con el suelo, combinando física realista con animación expresiva.
4. Codigo
```js
// --- Configuración Matter.js ---
let Engine = Matter.Engine,
  World = Matter.World,
  Bodies = Matter.Bodies,
  Body = Matter.Body,
  Mouse = Matter.Mouse,
  MouseConstraint = Matter.MouseConstraint;

let engine, world;
let boxes = [];
let palabra = "bounce";
let restPos = [];
let stretching = false;
let stretchAmount = 0;
let floor;
let releasing = false;
let releaseIndex = 0;
let lastReleaseTime = 0;

function setup() {
  createCanvas(800, 500);
  engine = Engine.create();
  world = engine.world;
  Engine.run(engine);

  // Crear piso (mitad de la pantalla)
  floor = Bodies.rectangle(width / 2, height / 2 + 120, width, 20, {
    isStatic: true,
    restitution: 1,
  });
  World.add(world, floor);

  // Crear letras centradas
  let totalWidth = palabra.length * 60;
  let startX = width / 2 - totalWidth / 2;

  for (let i = 0; i < palabra.length; i++) {
    let letra = palabra[i];
    let box = Bodies.rectangle(startX + i * 60, height / 2, 50, 80, {
      restitution: 0.8,
      friction: 0.2,
      isStatic: true,
    });
    box.label = letra;
    box.squash = 1; // Para el squash/stretch visual
    World.add(world, box);
    boxes.push(box);
    restPos.push({ x: box.position.x, y: box.position.y });
  }

  // Mouse setup
  let canvasmouse = Mouse.create(canvas.elt);
  canvasmouse.pixelRatio = pixelDensity();
  let options = { mouse: canvasmouse };
  let mConstraint = MouseConstraint.create(engine, options);
  World.add(world, mConstraint);
}

function draw() {
  background(245);



  textAlign(CENTER, CENTER);
  rectMode(CENTER);
  textSize(60);

  // Estiramiento mientras se mantiene presionado
  if (stretching) {
    stretchAmount = min(stretchAmount + 0.02, 0.6);
  } else {
    stretchAmount = max(stretchAmount - 0.05, 0);
  }

  // Si estamos soltando letras, hacerlo secuencialmente
  if (releasing && millis() - lastReleaseTime > 180) {
    if (releaseIndex < boxes.length) {
      let b = boxes[releaseIndex];
      Matter.Body.setStatic(b, false);
      Body.applyForce(b, b.position, { x: 0, y: -0.15 - stretchAmount * 0.8 });
      releaseIndex++;
      lastReleaseTime = millis();
    } else {
      releasing = false;
    }
  }

  // Dibujar letras con squash/stretch
  for (let i = 0; i < boxes.length; i++) {
    let b = boxes[i];

    // Detectar si está cerca del piso (para squash visual)
    let distToFloor = (height / 2 + 120) - b.position.y;
    if (distToFloor < 45 && b.velocity.y > 0) {
      b.squash = lerp(b.squash, 1.5, 0.3); // aplastamiento
    } else {
      b.squash = lerp(b.squash, 1, 0.1); // vuelve a la forma normal
    }

    push();
    translate(b.position.x, b.position.y);
    rotate(b.angle);

    // Aplicar squash/stretch
    scale(1.1 / b.squash, b.squash);

    // Cuerpo
    fill(30);
    rect(0, 0, 50, 80, 10);

    // Letra
    fill(255);
    text(b.label.toUpperCase(), 0, 10);
    pop();

    // Si la letra cae mucho, la regresamos
    if (!b.isStatic && b.position.y > height) {
      Matter.Body.setStatic(b, true);
      Matter.Body.setPosition(b, restPos[i]);
      Matter.Body.setVelocity(b, { x: 0, y: 0 });
      Matter.Body.setAngle(b, 0);
      b.squash = 1;
    }
  }
}

// --- Interacción ---
function mousePressed() {
  for (let b of boxes) {
    let d = dist(mouseX, mouseY, b.position.x, b.position.y);
    if (d < 50) {
      stretching = true;
      break;
    }
  }
}

function mouseReleased() {
  if (stretching) {
    stretching = false;
    releasing = true;
    releaseIndex = 0;
    lastReleaseTime = millis();
  }
}


```
5. Captura de pantalla:  
<img width="763" height="570" alt="image" src="https://github.com/user-attachments/assets/d48713c1-8962-4cb9-83f9-20a9ddc84258" />  


6. Link:  
>  [Apply 7] (https://editor.p5js.org/vlr1004/sketches/vOApDG6S1)

# Rubrica
## Nota propuesta 
5
## Defensa
**Actividad 1 – Análisis de ejemplos e ideas propias**
Merezco la calificación de 5 en esta actividad porque no solo analicé cuidadosamente los ejemplos del proyecto *Word as Image* de Ji Lee, sino que expliqué con claridad cómo cada composición logra transmitir visualmente el significado de la palabra. En cada análisis identifiqué los recursos tipográficos y espaciales utilizados (proporción, posición, forma y color), evidenciando comprensión del concepto de tipografía semántica. Además, propuse mis propias ideas con creatividad y coherencia, demostrando que puedo aplicar el mismo principio de forma original. Mi trabajo refleja observación crítica, entendimiento del propósito comunicativo y capacidad para generar propuestas visuales efectivas.

**Actividad 2 – Exploración y experimentación con Matter.js**
En esta actividad demostré un dominio técnico sólido al integrar Matter.js con p5.js mediante dos experimentos funcionales. Repliqué ejemplos básicos y los adapté, evidenciando comprensión de los conceptos fundamentales del motor físico (Engine, World, Bodies, Constraint, MouseConstraint). Documenté con claridad el código, las capturas y las explicaciones, lo que muestra una comprensión profunda del funcionamiento del motor y su relación con la simulación física. También reflexioné sobre las dificultades iniciales y cómo las resolví, lo que evidencia mi autonomía y capacidad para aprender de manera práctica. Cumplí con todos los requisitos y además integré elementos adicionales, como comentarios y organización del código, que mejoran su legibilidad.

**Actividad 3 – Animación tipográfica semántica**
Mi proyecto final integra de forma creativa los conocimientos de diseño tipográfico y simulación física aprendidos durante la unidad. La animación de la palabra “BOUNCE” refleja con precisión su significado mediante comportamientos físicos coherentes —elasticidad, rebote y squash & stretch—, combinando realismo físico con expresividad visual. El código demuestra control sobre las propiedades físicas, la interacción del usuario y la sincronización de los cuerpos, evidenciando dominio tanto técnico como conceptual. Además, la propuesta transmite de manera visual y lúdica el sentido de la palabra, cumpliendo y superando los criterios del enunciado. Por esto considero que mi trabajo alcanza el nivel más alto de desempeño y merece la nota máxima.
