# Unidad 2


## 🛠 Fase: Apply

1. Describe el concepto de tu obra generativa.  
  Quiero asemejar el movimiento continuio de las olas donde los que prueben la obra puedan interacturar con el oleaje.


2. ¿Cómo piensas aplicar el marco MOTION 101 y por qué?  
   Estoy aplicando Motion 101 al trabajar la posición, velocidad y aceleración de cada partícula de manera acumulativa. Cada partícula responde a fuerzas calculadas por algoritmos como Perlin Noise y atracción vectorial, lo que determina su aceleración. Luego, esa aceleración afecta la velocidad, y la velocidad cambia la posición, creando un movimiento fluido y orgánico que simula el comportamiento de las olas del mar.

   
4. ¿Qué algoritmo de aceleración vas a utilizar? ¿Por qué?  
Voy a utilizar un algoritmo basado en Perlin Noise combinado con atracción gravitacional hacia el mouse, donde las aceleraciones es generada por dos fuentes dinámicas: ruido perlin y la fuerza de atracción, porque quiero mostrar un poco de la naturalidad de las olas.
   
5. El contenido generado debe ser interactivo. Puedes utilizar mouse, teclado, cámara, micrófono, etc, para variar los parámetros del algoritmo en tiempo real.  
Voy a utilizar el mouse para interacturar de dos manera:
  - Movimiento de mouse: Atrae las particulas que forman las olas, deformando la ola en tiempo real.
  - Click Izquierdo: "Recoger" las olas, como si las olas regresaran a su estado de calma(?)

7. El código de la aplicación:
``` js
let waveParticles = [];
let numParticles = 300;
let noiseOffset = 0;
let attractor;

function setup() {
  createCanvas(800, 600);
  attractor = createVector(width / 2, height / 2);

  for (let i = 0; i < numParticles; i++) {
    waveParticles.push(new WaveParticle(i));
  }
}

function draw() {
  background(10, 40, 80);

  attractor.x = mouseX;
  attractor.y = mouseY;

  for (let p of waveParticles) {
    p.applyPerlinNoise();
    p.attract(attractor);
    p.update();
    p.display();
  }

  // Visualizar el punto de atracción (mouse)
  noStroke();
  fill(255, 100);
  ellipse(attractor.x, attractor.y, 20, 20);
}

function mousePressed() {
  if (mouseButton === LEFT) {
    resetWaves();
  }
}

function resetWaves() {
  for (let i = 0; i < waveParticles.length; i++) {
    let p = waveParticles[i];
    p.position = createVector(
      map(i, 0, numParticles - 1, 0, width),
      height / 2
    );
    p.velocity.mult(0); // Detener la velocidad
    p.acceleration.mult(0);
  }
}

class WaveParticle {
  constructor(index) {
    this.index = index;
    this.position = createVector(
      map(index, 0, numParticles - 1, 0, width),
      height / 2
    );
    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
    this.offset = random(1000);
  }

  applyPerlinNoise() {
    let noiseY = noise(this.offset + frameCount * 0.01);
    let waveHeight = map(noiseY, 0, 1, -50, 50);
    this.acceleration.y = waveHeight * 0.01;
  }

  attract(target) {
    let force = p5.Vector.sub(target, this.position);
    let distanceSq = constrain(force.magSq(), 100, 10000);
    let G = 50;
    let strength = G / distanceSq;
    force.setMag(strength);
    this.acceleration.add(force);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.velocity.mult(0.95);
    this.acceleration.mult(0);
  }

  display() {
    noStroke();
    fill(200, 220, 255, 150);
    ellipse(this.position.x, this.position.y, 5, 5);
  }
}
```
    
7. Un enlace al proyecto en el editor de p5.js.  
https://editor.p5js.org/vlr1004/sketches/1TEDzgGaF
8. Una captura de pantalla representativa de tu pieza de arte generativo.
<img width="1919" height="927" alt="image" src="https://github.com/user-attachments/assets/634cf03d-68cb-4391-b5a0-d57cdc15e135" />

