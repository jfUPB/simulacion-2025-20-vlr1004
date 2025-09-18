# Evidencias de la unidad 5

## Actividad 2
### ¿Cómo se está gestionando la creación y la desaparción de las partículas y cómo se gestiona la memoria en cada una de las simulaciones?
1. La gestión y creación de partículas esta propuesto como un ciclo de vida, que uitliza el arreglo particulas y un bucle que las renderiza, para eliminarlas la particula tiene un tiempo de vida que al terminar la coloca en un estado de 'muerte'. La gestion de la memoria se realiza primero asignandola cuando se crea una particula y se le adjunta las propiedades que tenga la particula; para su liberación, la particula debe de "morir", lo que se vuelve basura y el programa la libera ya que al no ser necesitada, es eliminada.
2. Para la creación de las particulas no se crea desde un pucle principal draw, sino que cada objeto 'emitter' crea una nueva particula en cada ciclo. Siendo el usuario el que crea el nuevo emisor y se questiona de manera independiente. Para la desaparción, la particula tiene un tiempo de vida que al acabarse, se coloca en el estado 'muerta' y se elimina. Para la gestón de memoria, has dos estados importantes, primero cuando se crea un nuevo emitter (al hacer clic con el ratón) y el segundo cuando cada emitter crea una nueva particula en su ciclo de vida. Para la liberación de la memoria de las particulas, al ser eliminadas no se concideran utilices por lo que se libera automaticamnte su memoria, contrario a los emitter que no tienen ninguna función para realizar esto. (Según mi amiga Geminis dice que consume mas memoria si el usuario hace muchos clics)
3. La creación de particulas, hay dos posibilidades, si es menor a 0.5 se crea una particula y si es mayor se crea un confeti siendo esta una clase hija de la particula, almacenadas en un array del emitter. Su desaparición se hace cuando su estado es de 'muerte' es verdadero (`p.isDead()`) llendo para atras, se van eliminando con `splice(i, 1)`. Para su memoria se elimina la memoria automaticamente con el garbage collector.
4. La creación de las particulas se realiza en el `draw()`, con la función `emitter.addParticle()`, para su desaparición al llegar el tiempo limite de su vida, es eliminada la aprticula. Se gestiona la memoria, primero asignando en la instancia de la particula, que se guardan el en arreglo de `this.particles` del objeto emitter y su liberación es con el recolector de basura de javaScript, y cuando se considera 'muerta', libera la memoria ocupada.
5. Para la creación de sus particulas se hace con el blucle `draw()` como en el anteriores. Para desaparecer lo mismo que el anterior donde el tiempo (su vida util) se va acabando (El bucle que maneja esta eliminación sigue iterando hacia atrás para mantener la estabilidad del índice del arreglo). Para la gestión de memoria, se asigna a las particulas en cada fotograma y el unico objeto repeller solo se crea una vez al inicio del programa, se eliminan/liberan, con el garbage colletor, liberando la memora automaticamente la particula pierde su vida util. 

### Experimentos:

## 1. 
- **Tema anterior utilizado:** Lévy flight
- **Explicación gestión:** - *Creación y Desaparición:* La lógica sigue siendo la misma. Las partículas se crean una por fotograma en draw() y se eliminan cuando su lifespan llega a cero.  - *Gestión de Memoria:* La memoria se gestiona de forma eficiente. Se asigna para cada nueva partícula, y el recolector de basura la libera automáticamente cuando la partícula es eliminada del arreglo, evitando la acumulación de objetos.
- **Explicación:** Queria que pareciera una colmena que va saliendo de su panal, que lo ayuda a dar el sistema de particulas al simular como salen las abejas y el lévy flight en parecerse al movimiento de las abejas 
- **Enlace:** https://editor.p5js.org/vlr1004/sketches/QXXVbur85
- **Cambios al codigo:**
**Sketch**
```js
let emitter;
let hive;

function setup() {
  createCanvas(640, 480);
  // El emisor de avispas se crea en el centro de la pantalla
  emitter = new Emitter(width / 2, height / 2);
  hive = createVector(width / 2, height / 2);
}

function draw() {
  background(255);
  // La "gravedad" ahora apunta hacia la colmena
  let attraction = p5.Vector.sub(hive, emitter.origin);
  attraction.setMag(0.1); 
  
  // Agregamos una nueva partícula
  emitter.addParticle();
  
  // Aplicamos la fuerza de atracción
  emitter.applyForce(attraction);
  
  // Aplicamos un pequeño 'Lévy flight' para el movimiento errático
  let erratic = p5.Vector.random2D().mult(0.5);
  emitter.applyForce(erratic);

  emitter.run();
}
```
**Emitter**
```js
class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];
      particle.run();
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}
```
**Particle**
```js
class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.lifespan = 255.0;
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
    this.lifespan -= 2;
  }

  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  isDead() {
    return this.lifespan < 0.0;
  }
}
```
- Captura:
<img width="587" height="432" alt="image" src="https://github.com/user-attachments/assets/07506b7a-76bb-491e-9fa1-e42d76648cc1" />



## 2. 
- **Tema anterior utilizado:** Random y vectores
- **Expliación gestión:** - **Creación:** Las partículas se generarán en la ubicación del `waterSource` (el hueco) con cada fotograma. - **Desaparición:** Las partículas seguirán teniendo una vida útil y serán eliminadas del arreglo cuando "mueran", permitiendo que el recolector de basura libere la memoria. - **Cambio de Hueco:** El cambio de la posición del hueco no será automático; en su lugar, ocurrirá cuando detectemos un clic del ratón dentro del área del `waterSource`. Esto añade un elemento de interactividad al proyecto.
- **Explicación:** Queria simular una fuga de agua, donde las particulas del programa son el agua, que cae descuidada e intentamos tapar pero salen hoyos de lugares randoms.
- **Enlace:** https://editor.p5js.org/vlr1004/sketches/aY9syG2uB 
- **Codigo:**
**Paarticle**
```js
class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.lifespan = 255.0;
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
    this.lifespan -= 2;
  }

  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    // Cambiamos el color de relleno a azul
    fill(0, 0, 255, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  isDead() {
    return this.lifespan < 0.0;
  }
}
```
Sketch
```js
let emitter;
let waterSource;
let waterSourceRadius = 20;

function setup() {
  createCanvas(640, 480);
  // El emisor se crea en el centro de la pantalla
  emitter = new Emitter(width / 2, height / 2);
  waterSource = createVector(width / 2, height / 2);
}

function draw() {
  background(255);

  // El emisor de partículas se mueve a la posición de la fuente de agua
  emitter.origin.set(waterSource.x, waterSource.y);
  
  // Se añade una nueva partícula en la posición de la fuente de agua
  emitter.addParticle();

  // Se aplican las fuerzas de movimiento
  let randomForce = p5.Vector.random2D();
  randomForce.mult(0.2);
  emitter.applyForce(randomForce);

  // Se ejecutan y se muestran las partículas
  emitter.run();
  
  // Dibujamos la fuente de agua para visualización
  fill(0, 150, 255);
  noStroke();
  circle(waterSource.x, waterSource.y, waterSourceRadius * 2);
}

function mousePressed() {
  // Verificamos si el clic del ratón está dentro del círculo de la fuente de agua
  let distance = dist(mouseX, mouseY, waterSource.x, waterSource.y);
  if (distance < waterSourceRadius) {
    // Si el clic está dentro, el hueco se mueve a una nueva posición aleatoria
    waterSource.set(random(width), random(height));
  }
}
```
Emitter
```js
class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];
      particle.run();
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}
```
- **Captura:**
<img width="646" height="322" alt="image" src="https://github.com/user-attachments/assets/0c9b778b-f6df-4f46-8d94-3a7fe8a5bb5e" />
 

## 3. 
- **Tema anterior utilizado:** Motion 101
- **Expliación gestión:** - **Creación:** El proceso de creación es continuo. En cada fotograma (o ciclo del bucle `draw`), se crea una nueva partícula y se añade a un arreglo llamado `particles` dentro de la clase Emitter. El código que lo hace es: `this.particles.push(new Particle(...))`. La partícula se crea con su propia posición, velocidad y otras propiedades iniciales. - **Desaparición:** La desaparición se basa en una condición de "muerte". En el código de la clase `Particle`, se define un método `isDead()` que verifica si la partícula ha salido de la pantalla por la parte inferior. Si la condición `(this.position.y > height + this.size)` es verdadera, la partícula se considera muerta. - El sistema asegura que la cantidad de memoria utilizada por el programa se mantenga estable. Las partículas nuevas reemplazan a las viejas, creando un flujo constante de asignación y liberación de memoria que evita que la simulación se vuelva lenta o colapse con el tiempo.
- **Explicación:** Al principio no sabía que hacer, pero queria seguir utilizando el agua como concepto, comencé haciendo una cascada de cosas (unas caritas felices) pero no me gusto, al ver el codigo en marcha me recordo a las gotas que caen por las ventanas de los carros.
- **Enlace:** https://editor.p5js.org/vlr1004/sketches/MuIwQM-Wb
- **Codigo:**
  **Emitter**
```js
class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];
      particle.run();
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}
```
**Particle**
```js
class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(0, 0);
    this.color = color(100, 150, 255, 150); 
    this.size = random(8, 15);
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
    // Hacemos que las gotas reboten suavemente en los bordes laterales
    if (this.position.x < 0 || this.position.x > width) {
        this.velocity.x *= -0.5;
    }
    this.position.x = constrain(this.position.x, 0, width);
  }

  show() {
    noStroke();
    fill(this.color);
    // Dibujamos la forma de gota
    ellipse(this.position.x, this.position.y, this.size, this.size * 1.5);
    // Añadimos un pequeño brillo para dar volumen
    fill(255, 255, 255, 80);
    circle(this.position.x - this.size * 0.1, this.position.y - this.size * 0.3, this.size * 0.4);
  }

  // La partícula muere si sale del lienzo por la parte inferior
  isDead() {
    return (this.position.y > height + this.size);
  }
}
```
**Sketch**
```js
let emitter;

function setup() {
  createCanvas(640, 480);
  emitter = new Emitter(width / 2, 0);
  background(20); 
}

function draw() {
  background(20, 50);

  emitter.origin.set(random(width), 0);
  
  emitter.addParticle();

  let gravity = createVector(0, 0.08);
  emitter.applyForce(gravity);

  let wind = createVector(random(-0.02, 0.02), 0);
  emitter.applyForce(wind);

  for (let particle of emitter.particles) {
    let friction = particle.velocity.copy();
    friction.mult(-1);
    friction.normalize();
    friction.mult(0.02);
    particle.applyForce(friction);
  }

  emitter.run();
}
```
- **Captura:**
<img width="555" height="433" alt="image" src="https://github.com/user-attachments/assets/34954b3a-5031-438c-a74e-f8bc9e59de32" />


## 4. 
- **Tema anterior utilizado:** Ruido perlin y ondas.
- **Expliación gestión:** - **Creación:** Se crea una nueva partícula en cada ciclo del draw() y se agrega a un emisor. -**Desaparición:** Las partículas son eliminadas cuando "mueren", en este caso, al salir de la pantalla. El recolector de basura de JavaScript se encarga de liberar su memoria. -**Control del Consumo de Memoria:** El programa evita el uso excesivo de memoria al eliminar constantemente las partículas antiguas, manteniendo un número estable de partículas en la simulación.
- **Explicación:** Al principio queria seguir con el tema del agua pero al realizar el codigo, me gusto como se veia, y ahora es una cuerda para dibujar ondas 
- **Enlace:** https://editor.p5js.org/vlr1004/sketches/XBJQZilHJ
- **Codigo:**
Sketch
```js
let emitter;
let t = 0; // Una variable de tiempo para el ruido Perlin

function setup() {
  createCanvas(640, 480);
  // El emisor se crea en la parte superior izquierda de la pantalla
  emitter = new Emitter(0, 0);
  background(255);
}

function draw() {
  // Oscurecemos el fondo gradualmente para un efecto de rastro
  background(255, 30);

  // La manguera sigue al ratón
  emitter.origin.set(mouseX, mouseY);
  
  // Se añade una nueva partícula
  emitter.addParticle();

  // Aplicamos un empuje basado en el ruido Perlin para un movimiento "orgánico"
  // Multiplicamos por 2 para que sea más notable
  let perlinForce = p5.Vector.fromAngle(noise(t) * TWO_PI).mult(2);
  emitter.applyForce(perlinForce);
  
  // Incrementamos el tiempo para el ruido Perlin
  t += 0.01;

  // Aplicamos una fuerza de gravedad
  let gravity = createVector(0, 0.05);
  emitter.applyForce(gravity);

  // Aplicamos una fuerza de fricción simple para simular la resistencia del aire
  for (let particle of emitter.particles) {
    let friction = particle.velocity.copy();
    friction.mult(-1);
    friction.normalize();
    friction.mult(0.01);
    particle.applyForce(friction);
  }

  // Se ejecutan y se muestran las partículas
  emitter.run();
}
```
Particle
```js
class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(0, 0);
    this.lifespan = 255.0;
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
  }

  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  isDead() {
    return (this.position.y > height + 20 || this.position.x > width + 20 || this.position.x < -20);
  }
}
```
Emitter
```js
class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];
      particle.run();
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}
```
- **Captura:**
<img width="520" height="422" alt="image" src="https://github.com/user-attachments/assets/35b54a58-84d6-485f-953d-2dac28a1f53e" />
 

## 5. 
- **Tema anterior utilizado:** distribución normal y aceleración
- **Expliación gestión:** -**Creación:** Una nueva partícula se crea en cada ciclo del draw() y se agrega a un emisor. -**Desaparición:** Las partículas son eliminadas cuando "mueren", al salir de la pantalla. El recolector de basura de JavaScript libera su memoria. -**Control del Consumo de Memoria:** El programa evita el uso excesivo de memoria eliminando constantemente las partículas antiguas, manteniendo un número estable de partículas en la simulación.
- **Explicación:** Queria experimentar con un arte mas abstracto y que se pudiera intervenir
- **Enlace:** https://editor.p5js.org/vlr1004/sketches/SERNfd9i3
- **Codigo:**
Sketch
```js
let emitter;
let mouseForce;

function setup() {
  createCanvas(640, 480);
  emitter = new Emitter(width / 2, height / 2);
  mouseForce = createVector(0, 0);
  background(255);
}

function draw() {
  background(255, 10);

  // La posición del emisor sigue la del ratón
  emitter.origin.set(mouseX, mouseY);
  
  // Se añade una nueva partícula
  emitter.addParticle();

  // Se calcula un vector de fuerza basado en una distribución normal
  // Esto crea un movimiento "agrupado" y no completamente aleatorio
  let x = randomGaussian();
  let y = randomGaussian();
  let sd = 1.5; // Desviación estándar
  let mean = 0; // Media
  
  // La fuerza del mouse controla la aceleración
  mouseForce.set(x * sd + mean, y * sd + mean);
  emitter.applyForce(mouseForce);

  // Se ejecutan y se muestran las partículas
  emitter.run();
}
```
Particle
```js
class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(0, 0);
    this.lifespan = 255.0;
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
  }

  show() {
    noStroke();
    // Color de la partícula basado en su "vida útil" para un efecto de desvanecimiento
    fill(0, 100, 255, this.lifespan);
    // Dibujamos un círculo pequeño y semitransparente
    circle(this.position.x, this.position.y, 4);
  }

  isDead() {
    // La partícula muere si está fuera de los límites de la pantalla
    return (this.position.y > height + 20 || this.position.x > width + 20 || this.position.x < -20 || this.position.y < -20);
  }
}
```
Emitter
```js
class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];
      particle.run();
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}
```
- **Captura:**
<img width="541" height="434" alt="image" src="https://github.com/user-attachments/assets/44dd88a2-c4a4-472e-a138-033555c8cae1" />
    


# Apply
## Actividad 3

### Concepto:
**Primero:** Arte desde la punta de tu dedo donde esté será el pincel.   
**Segundo:** Cesped para descansar, un relajante visualizador donde se interactua con el aire.  
**Tercero (El elegido):** Una fogata sonora, para relajar la mente con la muscia y las visuales.    

*Fogata Sonora*: La obra busca comunicar una sensación de calma, calidez y conexión con la música, como si estuvieras sentado frente a una fogata en una noche tranquila. El movimiento de las "llamas" es suave y constante, pero reacciona de manera perceptible a los cambios en la música, creando una experiencia interactiva y relajante.

### Herencia y Polimorfismo
Herencia: La clase `FireParticle` hereda de la clase base `Particle`. Esto permite que `FireParticle` obtenga propiedades y métodos de `Particle`, como la posición, velocidad, aceleración y el método `update()`, sin tener que reescribir ese código.

Polimorfismo: El método `show()` es un excelente ejemplo de polimorfismo. La clase base `Particle` tiene una versión simple del método, mientras que la clase hija `FireParticle` sobrescribe el método `show()` para dibujar una partícula con un color, un tamaño y una transparencia que se desvanecen, lo que le da su apariencia única de llama.

### Conceptos de Unidades Anteriores
- Motion 101: El movimiento de las partículas es suave y continuo. Aunque reaccionan a la música, el flujo general es fluido, lo que crea una sensación de calma y un movimiento natural, como el parpadeo de una llama real.

- Ruido Perlin: Se utiliza para generar el movimiento orgánico y tembloroso de las partículas, imitando el comportamiento errático pero fluido del fuego y el humo.

- Coordenadas Polares: Se utilizan para crear la fuerza de "parpadeo". En lugar de usar coordenadas `x` e `y` para la fuerza, se usa un ángulo generado por el ruido Perlin (`p5.Vector.fromAngle()`) para crear un vector de movimiento que se siente más circular y natural.

- Fuerza de Aceleración: La base de toda la dinámica de la obra. Tanto la fuerza horizontal (impulsada por los graves) como la fuerza vertical (crecimiento + agudos) se aplican como vectores de aceleración que afectan la velocidad de las partículas en cada fotograma.

  ### Gestión del Ciclo de vida y Memoria
- Creación de Partículas: Las partículas se crean al inicio de la obra y se regeneran continuamente en la parte inferior de la pantalla.

- Gestión de la Memoria: Las partículas que salen de la parte superior de la pantalla se eliminan (`particles.splice(i, 1)`), liberando recursos de la memoria y asegurando que la obra pueda funcionar de manera fluida por un tiempo indefinido.

### Interacción 
El usuario puede ingresar a la obra generativa, la canción de su elección.

### Código
- index
```js
<!DOCTYPE html>
<html>
  <head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.7.0/p5.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.7.0/addons/p5.sound.min.js"></script>
    <meta charset="utf-8" />
    <title>Fogata Sonora</title>
    <style>
      body {
        margin: 0;
        overflow: hidden;
      }
      #upload-container {
        position: absolute;
        top: 20px;
        left: 20px;
        background: rgba(255, 255, 255, 0.8);
        padding: 10px;
        border-radius: 5px;
        font-family: sans-serif;
      }
    </style>
  </head>
  <body>
    <div id="upload-container">
      <label for="file-input">Sube tu música:</label>
      <input type="file" id="file-input" accept="audio/*">
      <p id="status-message">Esperando que subas una canción...</p>
    </div>
    <script src="particle.js"></script>
    <script src="sketch.js"></script>
  </body>
</html>
```
- particle
```js
class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(0, 0);
    this.lifespan = 255.0; 
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
    this.lifespan -= 2.0;
  }

  show() {
    noStroke();
    fill(255, this.lifespan);
    circle(this.position.x, this.position.y, 4);
  }

  isDead() {
    return this.lifespan < 0.0;
  }
}

class FireParticle extends Particle {
  constructor(x, y, size) {
    super(x, y);
    this.size = size;
    this.color = color(random(200, 255), random(50, 150), 0);
  }

  show() {
    noStroke();
    let c = lerpColor(this.color, color(0, 0, 0, 0), 1 - this.lifespan / 255);
    fill(c);
    let currentSize = map(this.lifespan, 0, 255, 1, this.size);
    circle(this.position.x, this.position.y, currentSize);
  }

  isDead() {
    return this.position.y < -this.size;
  }
}
```
- sketch
```js
let particles = [];
let windStrength = 0.0;
let zoff = 0;
let song;
let amplitude;
let fft;
let fileInput;
let statusMessage;

function setup() {
  createCanvas(windowWidth, windowHeight);
  
  amplitude = new p5.Amplitude();
  fft = new p5.FFT();
  
  fileInput = select('#file-input');
  statusMessage = select('#status-message');
  
  fileInput.changed(handleFile);
}

function handleFile() {
  const file = fileInput.elt.files[0];
  if (file) {
    statusMessage.html('Cargando canción...');
    
    const url = URL.createObjectURL(file);
    
    loadSound(url, onSoundLoaded, onSoundError);
  }
}

function onSoundLoaded(loadedSound) {
  song = loadedSound;
  statusMessage.html('¡Canción cargada! La fogata está encendiéndose...');
  
  particles = [];
  
  // Aumentamos la cantidad de partículas a 1000 y el tamaño a un rango de (5, 12)
  for (let i = 0; i < 1000; i++) {
    particles.push(new FireParticle(random(width), height, random(5, 12)));
  }
  
  song.loop();
  userStartAudio();
}

function onSoundError() {
  statusMessage.html('Error al cargar la canción. Por favor, intenta de nuevo.');
  console.error('Error al cargar el archivo de audio.');
}

function draw() {
  background(0);

  if (song && song.isLoaded()) {
    let level = amplitude.getLevel();
    let spectrum = fft.analyze();
    let bassEnergy = fft.getEnergy('bass');
    let trebleEnergy = fft.getEnergy('treble');
    
    let bassInfluence = map(bassEnergy, 0, 255, -2, 2, true) + random(-0.5, 0.5); 
    
    let trebleInfluence = map(trebleEnergy, 0, 255, 0, -1, true); 
    
    if (trebleEnergy > 200) { 
      for (let i = particles.length - 1; i >= 0; i--) {
        let p = particles[i];
        let newParticle = new FireParticle(p.position.x, p.position.y, p.size * 1.5);
        newParticle.lifespan = p.lifespan / 2;
        newParticle.color = color(255, 204, 0);
        particles.push(newParticle);
      }
    }

    zoff += 0.005;

    for (let i = particles.length - 1; i >= 0; i--) {
      let p = particles[i];
      
      let horizontalForce = createVector(bassInfluence, 0);
      p.applyForce(horizontalForce);
      
      let x = map(p.position.x, 0, width, 0, 10);
      let y = map(p.position.y, 0, height, 0, 10);
      let angle = noise(x, y, zoff) * TWO_PI;
      let flickerForce = p5.Vector.fromAngle(angle);
      flickerForce.mult(0.5);
      p.applyForce(flickerForce);

      let growth = createVector(0, -0.1 + trebleInfluence); 
      p.applyForce(growth);
      
      p.run();

      if (p.isDead()) {
        particles.splice(i, 1);
        particles.push(new FireParticle(random(width), height, random(5, 12)));
      }
    }
  } else {
    fill(255);
    textAlign(CENTER);
    textSize(20);
    text('Por favor, sube una canción para encender la fogata.', width / 2, height / 2);
  }
}
```
- style
```js
html, body {
  margin: 0;
  padding: 0;
  overflow: hidden; /* Evita barras de desplazamiento */
}
canvas {
  display: block;
}
#status { 
  position: absolute;
  top: 10px;
  left: 10px;
  color: white;
  font-family: monospace;
  font-size: 1.2em;
  background-color: rgba(0, 0, 0, 0.5);
  padding: 5px 10px;
  border-radius: 5px;
  z-index: 1000; /* Asegura que esté por encima de todo */
}
```

### Enlace
https://editor.p5js.org/vlr1004/sketches/jKvpUxq-g

### Captura de Pantalla 
<img width="895" height="622" alt="image" src="https://github.com/user-attachments/assets/8fed1012-40d3-45b5-b2fd-09269e0400f5" />


### Auto calificación

**Propuesta de Nota:** 4.9 / 5.0
Considero que el proyecto merece una nota cercana a la máxima, ya que cumple con todos los criterios de la rúbrica de forma excelente, mostrando atención al detalle y una comprensión profunda de los conceptos.

**Justificación por Criterio**
*1. Investigación y Experimentación (Evidencia en la Bitácora)*
Nota Propuesta: 5.0 / 5.0

A lo largo de nuestra bitácora, la evidencia demuestra que el proceso fue un ciclo constante de experimentación y justificación.

Análisis de conceptos: Se analizó el error EncodingError: Unable to decode audio data, se diagnosticó el problema con el formato de audio y se propuso una solución que funcionó. Esto demuestra un análisis de un problema técnico.

Justificación de modificaciones: Cada cambio en el código fue justificado. Por ejemplo, al cambiar de un "prado en el viento" a una "fogata", se explicaron las razones estéticas y técnicas para modificar el fondo a negro, la paleta de colores a tonos cálidos y el movimiento de las partículas.

Interacción de conceptos: Se justificó cómo los conceptos de Ruido Perlin, Coordenadas Polares y Fuerza de Aceleración interactúan entre sí para crear el movimiento orgánico de la fogata, a diferencia de un movimiento puramente lineal. Se argumentó por qué era necesario separar la fuerza horizontal (graves) de la fuerza de "parpadeo" (Ruido Perlin) para corregir el sesgo en el movimiento.

*2. Intención y Diseño (Proceso de la Bitácora)*
Nota Propuesta: 4.5 / 5.0

La bitácora muestra una intención de diseño clara, aunque la fase de "bocetos" se realizó de forma verbal y no visual.

Concepto Definido: El concepto de "Fogata Sonora" se definió explícitamente y se mantuvo a lo largo de las modificaciones. El objetivo era evocar calma y conexión a través de la música.

Toma de Decisiones: Cada decisión, como el fondo negro, los colores cálidos, el movimiento suave y la reacción a los graves y agudos, se vincula directamente al concepto de una fogata que se siente personal y relajante.

Propósito de la Interacción: La interacción de subir una canción no es solo una funcionalidad, sino que tiene el propósito de hacer la obra única para cada usuario y hacer que el fuego baile al ritmo de la música elegida.

*3. Aplicación Técnica (Código de la Bitácora)*
Nota Propuesta: 4.8 / 5.0

El código final es robusto, modular y gestiona la memoria de manera eficiente.

Estructura Modular: El código está organizado en dos clases (Particle y FireParticle) y un archivo principal (sketch.js). Esto demuestra una estructura modular y una correcta aplicación de la programación orientada a objetos.

Herencia y Polimorfismo: FireParticle hereda de Particle, reusando código, y sobrescribe el método show() para un comportamiento visual único, lo que evidencia el uso de polimorfismo.

Gestión de Memoria: El programa gestiona activamente la memoria. Las partículas que salen de la pantalla se eliminan (splice) y se crean nuevas, manteniendo un número controlado y constante de partículas para garantizar un rendimiento estable.

*4. Calidad de la Obra Final (Artefacto Entregado)*
Nota Propuesta: 5.0 / 5.0

La calidad de la obra final es muy alta, mostrando una gran atención al detalle.

Estabilidad y Rendimiento: La obra se ejecuta de forma estable, sin caídas de frame rate, incluso con una gran cantidad de partículas.

Respuesta Interactiva: La respuesta a la música es predecible y consistente. El movimiento horizontal y el "pulso" de los agudos son claros y se sienten naturales, lo que hace que la obra sea agradable de ver.

Coherencia Visual: A pesar de los múltiples cambios y la complejidad del código, el resultado visual final es coherente con el concepto de fogata, con movimientos suaves, colores que se desvanecen y un aspecto visual que evoca calma y serenidad.

Nota: De igual forma, pienso que para mi elección cambia por la nota máxima 5.0, basándose en la superación de los desafíos técnicos y la aplicación exitosa de todos los conceptos de la rúbrica. El proceso de depuración del código y los ajustes continuos, como corregir el movimiento inclinado y mejorar la reacción a la música, me permitieron consolidar mi aprendizaje. La obra final es un reflejo directo de esta dedicación, mostrando una implementación técnica sólida que se mantiene fiel a mi visión creativa de una "fogata sonora" inmersiva y tranquila.

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/7963da09-8f32-4452-929d-5dd8a2f307dd" />
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/6af1d175-416a-4cb6-b8af-cf292be59879" />
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/52b65df5-f9a4-48a9-badd-2b6c7db9d5b9" />
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/070cde1b-081c-4846-a77b-0e544e062b7c" />

