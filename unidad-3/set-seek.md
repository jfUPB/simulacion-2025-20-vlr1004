# Unidad 3

## 🔎 Fase: Set + Seek


# Fricción 
## 1. Explica cómo modelaste cada fuerza.

*El carrito tendrá:*
- Posición y velocidad en un eje principal.
- Fricción constante que reduce la velocidad gradualmente.

*El click izquierdo:*
- Aumenta una fuerza de impulso mientras lo mantienes presionado.
- Al soltar, aplica ese impulso en una dirección.

*Las curvas:*
- Se pueden simular con cambios en la posición Y usando una función sinusoidal basada en X.
   
## 2. Conceptualmente cómo se relaciona la fuerza con la obra generativa:  
Es la resistencia que tiene un objeto con respecto a su movimiento. Siendo un carrito 


## 3. Copia el enlace a tu ejemplo en p5.js.
https://editor.p5js.org/vlr1004/sketches/XU4RZbAe4

## 4. Copia el código.
````js
let carrito;
let camino = [];

function setup() {
  createCanvas(800, 400);
  carrito = new Carrito();

  // Generar camino con curvas y rectas aleatorias
  let x = 0;
  let y = height / 2;
  while (x < width) {
    let tipo = random() < 0.5 ? "recta" : "curva"; // 50% recta, 50% curva
    let paso = random(40, 80); // ancho del segmento
    if (tipo === "recta") {
      for (let i = 0; i < paso; i++) {
        camino.push(createVector(x + i, y));
      }
    } else {
      let amplitud = random(20, 50);
      let frecuencia = random(0.05, 0.1);
      for (let i = 0; i < paso; i++) {
        let ny = y + sin((x + i) * frecuencia) * amplitud;
        camino.push(createVector(x + i, ny));
      }
    }
    x += paso;
  }
}

function draw() {
  background(220);

  // Dibujar camino
  stroke(100);
  strokeWeight(4);
  noFill();
  beginShape();
  for (let p of camino) {
    vertex(p.x, p.y);
  }
  endShape();

  // Si el mouse está presionado, dar más impulso
  if (mouseIsPressed) {
    carrito.applyForce(createVector(0.1, 0));
  }

  // Actualizar y mostrar carrito
  carrito.update();
  carrito.show();
}

class Carrito {
  constructor() {
    this.position = createVector(0, height / 2);
    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
    this.mass = 1;
  }

  applyForce(force) {
    let f = p5.Vector.div(force, this.mass);
    this.acceleration.add(f);
  }

  update() {
    // Fricción (opuesta a la velocidad)
    let friction = this.velocity.copy();
    if (friction.mag() > 0) {
      friction.normalize();
      friction.mult(-1); // Dirección opuesta
      let c = 0.05; // Coeficiente de fricción
      friction.mult(c);
      this.applyForce(friction);
    }

    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);

    // Limitar a pista
    this.position.x = constrain(this.position.x, 0, camino.length - 1);
    this.position.y = camino[int(this.position.x)].y; // seguir pista
  }

  show() {
    push();
    translate(this.position.x, this.position.y);
    fill(200, 0, 0);
    stroke(0);
    rectMode(CENTER);
    rect(0, 0, 30, 15);
    pop();
  }
}

````
## 5. Imagen

<img width="947" height="658" alt="image" src="https://github.com/user-attachments/assets/a03fb69c-ad4f-4bff-94cd-cfc5e70bc5cc" />

# Resistencia del aire y de fluidos
## 1. Explica cómo modelaste cada fuerza.
*El globo sube por:*
- Una fuerza de flotación constante (como aire caliente).
- Una resistencia proporcional al cuadrado de la velocidad (drag = k * v^2).

*El usuario:*
- Puede dar más viento con las flechas o teclas.
- Si el viento es fuerte, el globo sube más rápido, pero la resistencia también aumenta.

## 2. Conceptualmente cómo se relaciona la fuerza con la obra generativa:  
Es la resistencia que tiene un globo aereostatico en subir al "cielo" con respecto a las gotas de agua.


## 3. Copia el enlace a tu ejemplo en p5.js.
https://editor.p5js.org/vlr1004/sketches/UxmIo9DEu


## 4. Copia el código.

````js
let globo;
let gotas = [];

function setup() {
  createCanvas(400, 600);
  globo = new Globo();

  // Crear gotas de agua
  for (let i = 0; i < 50; i++) {
    gotas.push(new Gota());
  }
}

function draw() {
  background(200, 220, 255);

  // Actualizar gotas
  for (let g of gotas) {
    g.update();
    g.show();
  }

  // Controles con W y S
  if (keyIsDown(87)) { // W
    globo.applyForce(createVector(0, -0.2));
  }
  if (keyIsDown(83)) { // S
    globo.applyForce(createVector(0, 0.2));
  }

  // Resistencia del aire/fluidos
  let c = 0.1;
  let speed = globo.velocity.mag();
  let dragMagnitude = c * speed * speed;
  let drag = globo.velocity.copy();
  drag.mult(-1);
  drag.setMag(dragMagnitude);
  globo.applyForce(drag);

  globo.update();
  globo.show();
}

class Globo {
  constructor() {
    this.position = createVector(width / 2, height / 2);
    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
    this.mass = 1;
  }

  applyForce(force) {
    let f = p5.Vector.div(force, this.mass);
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);

    // Limites
    this.position.y = constrain(this.position.y, 50, height - 50);
  }

  show() {
    push();
    translate(this.position.x, this.position.y);
    fill(255, 100, 100);
    stroke(0);
    ellipse(0, 0, 60, 80); // globo
    fill(150, 75, 0);
    rect(-5, 35, 10, 15); // canasta
    pop();
  }
}

class Gota {
  constructor() {
    this.reset();
  }

  reset() {
    this.x = random(width);
    this.y = random(height, height + 200);
    this.size = random(3, 6);
    this.speed = random(1, 3);
  }

  update() {
    this.y -= this.speed;
    if (this.y < -10) {
      this.reset();
    }
  }

  show() {
    fill(0, 100, 255, 150);
    noStroke();
    ellipse(this.x, this.y, this.size, this.size * 1.5);
  }
}


````


## 5. Captura una imagen representativa de tu ejemplo.
<img width="1891" height="773" alt="image" src="https://github.com/user-attachments/assets/89b835a4-ce60-489b-8d06-15670048340a" />



# Atracción gravitacional
## 1. Explica cómo modelaste cada fuerza.
*Un árbol fijo (o que se mueva suavemente con el mouse).*
*Manzanas:*
- Posición y velocidad.
- Gravedad constante hacia abajo.

*El usuario:*
- Puede mover el tronco y provocar que algunas manzanas se desprendan y caigan.
- Colisión con el suelo para que las manzanas reboten o se detengan.

## 2. Conceptualmente cómo se relaciona la fuerza con la obra generativa.
Es la resistencia que tiene un cuerpo con respecto a la gravedad. La manzana cae porque es un objeto que esta siendo atraido por la gravedad de la tierra.

## 3. Copia el enlace a tu ejemplo en p5.js.
https://editor.p5js.org/vlr1004/sketches/0vxbdfuSr


## 4. Copia el código.
````js
let arbol;
let manzanas = [];
let gravedadActiva = false;

function setup() {
  createCanvas(800, 600);
  arbol = new Arbol(width / 2, height / 2 + 100);

  generarManzanas(6); // 6 manzanas iniciales
}

function draw() {
  background(200, 230, 255);

  arbol.show();

  for (let m of manzanas) {
    if (!m.attached && gravedadActiva) {
      // Aplica fuerza gravitacional hacia abajo
      let gravity = createVector(0, 0.2 * m.mass);
      m.applyForce(gravity);

      m.update();
    }
    m.show();
  }
}

function mousePressed() {
  if (mouseButton === LEFT) {
    // Suelta las manzanas actuales
    for (let m of manzanas) {
      m.attached = false;
    }
    gravedadActiva = true;

    // Genera nuevas manzanas en la copa
    generarManzanas(6);
  }
}

function generarManzanas(cantidad) {
  for (let i = 0; i < cantidad; i++) {
    // Posición aleatoria dentro de la copa del árbol
    let angle = random(TWO_PI);
    let radius = random(20, 80); // Radio dentro de la copa
    let offsetX = cos(angle) * radius;
    let offsetY = sin(angle) * radius * 0.8; // Aplastar para que sea elipse
    manzanas.push(new Manzana(arbol.x + offsetX, arbol.y - 100 + offsetY, createVector(offsetX, offsetY)));
  }
}

class Arbol {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  show() {
    // Tronco
    fill(120, 70, 20);
    rectMode(CENTER);
    rect(this.x, this.y, 40, 150);

    // Copa
    fill(30, 150, 60);
    ellipse(this.x, this.y - 100, 200, 180);
  }
}

class Manzana {
  constructor(x, y, offset) {
    this.pos = createVector(x, y);
    this.offset = offset;
    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
    this.mass = 5;
    this.r = 15;
    this.attached = true;
  }

  applyForce(force) {
    let f = p5.Vector.div(force, this.mass);
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.pos.add(this.velocity);
    this.acceleration.mult(0);
  }

  show() {
    if (this.attached) {
      // Mantener pegada al árbol
      this.pos.x = arbol.x + this.offset.x;
      this.pos.y = arbol.y - 100 + this.offset.y;
    }
    fill(255, 0, 0);
    noStroke();
    ellipse(this.pos.x, this.pos.y, this.r * 2);
    // Tallo
    stroke(0);
    strokeWeight(2);
    line(this.pos.x, this.pos.y - this.r, this.pos.x, this.pos.y - this.r - 5);
  }
}



````

## 5. Captura una imagen representativa de tu ejemplo.
<img width="1863" height="663" alt="image" src="https://github.com/user-attachments/assets/310cf9e8-52c8-448c-8957-ef442c98e169" />


