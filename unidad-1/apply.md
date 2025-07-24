# Unidad 1

## 🛠 Fase: Apply

# Actividad 8
- Introducción:
  -  Ver1: la obra es una simulacion de piano con el teclado, donde las notas musicales más gruesas (bajas) muestren en pantalla flores que crecen o se despliegan orgánicamente y las agudas (altas) estrellas que se mueven o brillan en el espacio. Utilizando conceptos de Lévy flight (para trayectorias orgánicas), distribucion normal (para posiciones iniciales) y ruido perlin (para animaciones suaves).
  -  Ver2: Hacer una obra simulando un piano pero combirtiendo la escala musical en una transformación visual progresiva, donde cada nota ocupa un lugar dentro de un espectro morfológico: desde las circulos (graves) hasta las estrellas (agudas), pasando por formas intermedias. Utilizando conceptos de Lévy flight (para trayectorias orgánicas), distribucion normal (para posiciones iniciales) y ruido perlin (para animaciones suaves).
- Código:
  - Ver 1:

```js
let particles = [];

function setup() {
  createCanvas(windowWidth, windowHeight);
  background(10);
  noStroke();
}

function draw() {
  background(10, 20); // deja un rastro visual
  for (let p of particles) {
    p.update();
    p.display();
  }
}

// Evento: presionar una tecla
function keyPressed() {
  let freq = keyToFreq(key);
  if (freq) {
    let type = freq < 300 ? 'flower' : 'star'; // grave <300Hz = flor, aguda = estrella
    particles.push(new Particle(type, freq));
  }
}

// Mapeo simple de teclas a frecuencia (puedes personalizarlo)
function keyToFreq(k) {
  let mapKeys = {
    'a': 130, 's': 146, 'd': 164, 'f': 174, 'g': 196, 'h': 220, 'j': 246,
    'q': 261, 'w': 293, 'e': 329, 'r': 349, 't': 392, 'y': 440, 'u': 493
  };
  return mapKeys[k.toLowerCase()] || null;
}

// Clase para partículas generadas por teclas
class Particle {
  constructor(type, freq) {
    this.type = type;
    this.freq = freq;

    // Posición inicial distribuida normalmente
    this.pos = createVector(
      randomGaussian(width / 2, width / 6),
      randomGaussian(height / 2, height / 6)
    );

    this.vel = p5.Vector.random2D().mult(random(1, 2));
    this.acc = createVector();
    this.noiseOffset = random(1000);
    this.size = map(freq, 130, 500, 10, 40);
    this.life = 255;
  }

  update() {
    // Movimiento tipo Lévy flight
    if (random(1) < 0.05) {
      let stepSize = pow(random(1), -1.5) * 10;
      let angle = random(TWO_PI);
      this.vel.add(p5.Vector.fromAngle(angle).mult(stepSize));
    }

    // Perlin para suavizar la dirección
    let n = noise(this.noiseOffset);
    let angle = n * TWO_PI;
    this.acc = p5.Vector.fromAngle(angle).mult(0.05);
    this.vel.add(this.acc);
    this.vel.limit(3);
    this.pos.add(this.vel);

    this.life -= 2;
    this.noiseOffset += 0.01;
  }

  display() {
    push();
    translate(this.pos.x, this.pos.y);
    if (this.type === 'flower') {
      fill(200, 100, 150, this.life);
      drawFlower(this.size);
    } else {
      fill(150, 200, 255, this.life);
      drawStar(this.size);
    }
    pop();
  }
}

// Dibuja una flor básica con pétalos
function drawFlower(size) {
  let petals = int(random(5, 8));
  for (let i = 0; i < petals; i++) {
    let angle = TWO_PI / petals * i;
    let x = cos(angle) * size;
    let y = sin(angle) * size;
    ellipse(x, y, size / 2, size);
  }
  fill(255, 200);
  ellipse(0, 0, size / 2); // centro
}

// Dibuja una estrella de 5 puntas
function drawStar(radius) {
  beginShape();
  for (let i = 0; i < 10; i++) {
    let angle = TWO_PI / 10 * i;
    let r = i % 2 === 0 ? radius : radius / 2;
    vertex(cos(angle) * r, sin(angle) * r);
  }
  endShape(CLOSE);
}
```
  - Ver 2:

``` js
let forms = [];
const keys = ['a', 's', 'd', 'f', 'g', 'h', 'j', 'q', 'w', 'e', 'r', 't', 'y', 'u'];

function setup() {
  createCanvas(windowWidth, windowHeight);
  angleMode(DEGREES);
  noStroke();
}

function draw() {
  background(10, 30);

  for (let i = forms.length - 1; i >= 0; i--) {
    forms[i].update();
    forms[i].display();

    if (forms[i].life <= 0) {
      forms.splice(i, 1);
    }
  }
}

function keyPressed() {
  let k = key.toLowerCase();
  let index = keys.indexOf(k);
  if (index !== -1) {
    let t = index / (keys.length - 1); // índice 0 (estrella) a 1 (círculo)
    forms.push(new MorphingShape(t));
  }
}

class MorphingShape {
  constructor(t) {
    this.t = t;
    // DISTRIBUCIÓN NORMAL para posición inicial
    this.pos = createVector(
      randomGaussian(width / 2, width / 6),
      randomGaussian(height / 2, height / 6)
    );
    this.vel = p5.Vector.random2D().mult(random(0.5, 2));
    this.acc = createVector();
    this.size = lerp(40, 80, 1 - t);
    this.life = 255;
    this.rotation = random(360);
    this.rotationNoise = random(1000); // para suavizar rotación
    this.noiseOffset = random(1000);   // para dirección con ruido
  }

  update() {
    // LÉVY FLIGHT ocasional
    if (random(1) < 0.03) {
      let step = pow(random(1), -1.5) * 10;
      let angle = random(TWO_PI);
      this.vel.add(p5.Vector.fromAngle(angle).mult(step));
    }

    // Movimiento suave con ruido Perlin
    let nAngle = noise(this.noiseOffset) * TWO_PI * 2;
    this.acc = p5.Vector.fromAngle(nAngle).mult(0.2);
    this.vel.add(this.acc);
    this.vel.limit(3);
    this.pos.add(this.vel);

    // Rotación con ruido Perlin
    this.rotation += map(noise(this.rotationNoise), 0, 1, -2, 2);
    this.rotationNoise += 0.01;
    this.noiseOffset += 0.01;

    this.life -= 1.5;
  }

  display() {
    push();
    translate(this.pos.x, this.pos.y);
    rotate(this.rotation);

    // Forma interpolada entre estrella y círculo
    let points = 5;
    let smoothness = lerp(0.0, 1.0, this.t); // 0 = estrella, 1 = círculo
    let r1 = this.size;
    let r2 = lerp(r1 * 0.5, r1, smoothness);

    let col = lerpColor(color(255, 220, 100), color(100, 180, 255), this.t);
    col.setAlpha(this.life);
    fill(col);

    beginShape();
    let total = points * 2;
    for (let i = 0; i < total; i++) {
      let angle = map(i, 0, total, 0, 360);
      let r = i % 2 === 0 ? r1 : r2;
      r = lerp(r, r1, smoothness);
      let x = cos(angle) * r;
      let y = sin(angle) * r;
      vertex(x, y);
    }
    endShape(CLOSE);
    pop();
  }
}

```

- Enlace:
  - Ver 1: https://editor.p5js.org/vlr1004/sketches/lHf0Shhuk
  - Ver 2: https://editor.p5js.org/vlr1004/sketches/DXE4xZUqm
- Captura:
  - Ver 1:
<img width="1919" height="855" alt="image" src="https://github.com/user-attachments/assets/c5b4561c-9d4a-4c7c-a473-04e661bab7a9" />

  - Ver 2:
<img width="1919" height="910" alt="image" src="https://github.com/user-attachments/assets/705d3086-f06c-4296-981a-db60cbc49cef" />

