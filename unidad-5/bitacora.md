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
- **Tema anterior utilizado:**
- **Expliación gestión:**
- **Explicación:**
- **Enlace:**
- **Codigo:**
```js
```
- **Captura:**    





