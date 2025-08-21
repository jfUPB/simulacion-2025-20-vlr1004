# Unidad 3


## 🛠 Fase: Apply

### Diseña e implementa tu obra generativa interactiva en tiempo real.

- Debe ser interactiva.
- Debes usar al menos dos algoritmos diferentes de la unidad 1, además de random.
- Explica cómo modelaste el problema de los n-cuerpos en tu obra.

Mi obra esta inspirada en el sistema solar, el como se mueve y al llegar un objeto extraño como se comporta. 

### Copia el enlace a tu simulación en p5.js.

https://editor.p5js.org/vlr1004/sketches/zttQUDgp4

### Copia el código.
## Mover


// Gravitational Attraction + Calder-like visuals
class Mover {
  constructor(x, y, vx, vy, m, shapeType) {
    this.pos = createVector(x, y);
    this.vel = createVector(vx, vy);
    this.acc = createVector(0, 0);
    this.mass = m;
    this.r = sqrt(this.mass) * 2;
    this.shape = shapeType; // "circle" o "line"
    this.noiseOffset = random(1000);
  }

  applyForce(force) {
    let f = p5.Vector.div(force, this.mass);
    this.acc.add(f);
  }

  attract(mover) {
    let force = p5.Vector.sub(this.pos, mover.pos);
    let distanceSq = constrain(force.magSq(), 100, 1000);
    let G = 1;
    let strength = (G * (this.mass * mover.mass)) / distanceSq;
    force.setMag(strength);
    mover.applyForce(force);
  }

  update() {
    // viento suave con ruido Perlin
    let angle = noise(this.noiseOffset) * TWO_PI * 2;
    let wind = p5.Vector.fromAngle(angle).mult(0.05);
    this.applyForce(wind);
    this.noiseOffset += 0.01;

    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.set(0, 0);
  }

  show() {
    stroke(0);
    strokeWeight(2);
    if (this.shape === "circle") {
      fill(200, 100, 150, 150);
      ellipse(this.pos.x, this.pos.y, this.r * 2);
    } else if (this.shape === "line") {
      fill(100, 200, 255, 150);
      line(this.pos.x - this.r, this.pos.y - this.r,
           this.pos.x + this.r, this.pos.y + this.r);
    }
  }
}



## Sketch

let movers = [];
let sun;

function setup() {
  createCanvas(800, 600);

  // Inicializar 50 partículas orbitando
  for (let i = 0; i < 50; i++) {
    let pos = p5.Vector.random2D();
    let vel = pos.copy();
    vel.setMag(random(5, 10));
    pos.setMag(random(100, 200));
    vel.rotate(PI / 2);
    let m = random(5, 20);
    let shape = random() < 0.5 ? "circle" : "line";
    movers[i] = new Mover(pos.x, pos.y, vel.x, vel.y, m, shape);
  }

  // Sol central
  sun = new Mover(0, 0, 0, 0, 500, "circle");
}

function draw() {
  background(255, 40);
  translate(width / 2, height / 2);

  // atracción del sol a cada partícula
  for (let mover of movers) {
    sun.attract(mover);

    // atracción mutua (problema de los n-cuerpos)
    for (let other of movers) {
      if (mover !== other) {
        mover.attract(other);
      }
    }
  }

  // actualizar y mostrar
  for (let mover of movers) {
    mover.update();
    mover.show();
  }

  // opcional: mostrar el sol
  noStroke();
  fill(255, 204, 0, 150);
  ellipse(sun.pos.x, sun.pos.y, 40);
}

// --- interacción con click ---
function mousePressed() {
  if (mouseButton === LEFT) {
    // Lévy flight para velocidad inicial
    let step = random() < 0.1 ? random(30, 80) : random(2, 10);
    let angle = random(TWO_PI);
    let vx = cos(angle) * step;
    let vy = sin(angle) * step;

    // nuevo mover en posición del mouse
    let mx = mouseX - width / 2;
    let my = mouseY - height / 2;
    let m = random(5, 15);
    let shape = random() < 0.5 ? "circle" : "line";

    movers.push(new Mover(mx, my, vx, vy, m, shape));
  }
}


### Captura una imagen representativa de tu ejemplo.
<img width="1005" height="768" alt="image" src="https://github.com/user-attachments/assets/710fc3fb-3b27-46d9-a2a7-868cb30239d5" />

