# Evidencias de la unidad 8

# Actividad 1
1. Describe tus observaciones sobre la conexión sonido-imagen en al menos dos de las performances vistas.
> Carles Viarnès & Alba G. Corral 360º AV Show y Dimension N - Alba G Corral [ES] & Makaruk. Siento que la mayor conexión que tienen las sobras son el movimiento que tienen dependiendo de como este sonando, si suena bajito, normalmente se apaciguan los movimientos contrario a cuando esta más elevada la musica, pero tambien hace lo contraio a veces dando así el protagonismo a la voz más que a lo visual. Creo que depende más de lo que quiera dar a entender la artista, sus sentimientos del momentos y sus pensamientos.
2. Explica qué elementos te parecieron generativos y por qué crees que cada visualización sería única.
> Las formas en la que aparecen las figuras, su manera unica de aparecer o moverse en diferentes direcciones. La paleta de colores, depende de su presentación note que utiliza una paleta de colores pero no le quita lo generativo, ya que varia entre esos colores para así dejar un mensaje. 
3. Comparte tu reflexión sobre la sensación de “liveness”.
> Me gusta que el arte tome otra forma, es una gran muestra artistica ya que siempre se encontrara una manera diferente de realizar arte. Siento que tiene una conexión diferente, algo especial, que cada función sea diferente. Lo unico y diferente es despreciable a veces, pero reconocido por montenes; aunque mi cerebro a solo este mayormente concentrado en lo visual, el el fondo con la muscia se siente increible como se acompañan y viceversa.


# Actividad 2
1. La pieza musical elegida (con enlace/archivo si es posible).
> https://www.youtube.com/watch?v=pCBgcBXxCsI&list=RDpCBgcBXxCsI&start_radio=1
2. La descripción de tu concepto visual.
> Quiero seguir el concepto de la canción. DPR Ian lleva un mundo caotico lleno de reflejos, una paleta de colores brillante pero oscura. Quiero que sea como un mundo caotico lleno de dulces, que reflejen con el color lo bueno y lo malo, un mundo sin reglas pero que a la vez las necesita. 
3. Los inputs seleccionados y la justificación de por qué los elegiste.
> Teclado (flechas): quiero utilizarlos para cambiar del lado oscuro al "orden".  
> Teclado (Tecla A): creación de particulas.     
> Mouse: para manejar el movimiento de las particulas, y dirigir el curso del caos.  
> Camara (no creo utilizarla pero si lo logro la usare): Para generar reflejos, destruir particulas o moverlas caoticamente.  
4. ¿Qué algoritmos o técnicas planeas usar (ej: flow fields, flocking, física, partículas, etc.) y por qué?
> Sistema de partículas: Para representar los dulces y elementos volando. Se comportan de forma caótica y responden al audio  
> Flow fields (campos vectoriales): Para dar dirección orgánica al caos. Muestran que hay reglas invisibles detrás del desorden  
> Flocking (comportamiento tipo boids): Para crear agrupaciones de caramelos/criaturas. Simboliza el balance entre libertad y necesidad de orden  
> Simulación de física (Foces): Gravedad, colisiones y rebotes. Hace que los dulces se sientan más reales y pesados o ligeros según el mood  
> Noise (Perlin o Simplex Noise): Para distorsiones y movimiento ondulante. Aporta esa sensación de inestabilidad emocional del mundo
5. Tus bocetos y una explicación de cómo los inputs influirán en los visuales.
> <img width="947" height="707" alt="image" src="https://github.com/user-attachments/assets/b5f1965f-a1a1-4947-ae8a-1b0fc6ee755c" />


# Actividad 3
1. El código fuente completo de tu sketch en p5.js.
> html
``` js
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Mundo Caótico de Dulces</title>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/addons/p5.sound.min.js"></script>
        <script src="sketch.js"></script>
    <style>
      body {
        margin: 0;
        overflow: hidden;
        background-color: black;
        color: white;
        font-family: monospace;
      }
      .instructions {
        position: absolute;
        top: 10px;
        left: 10px;
        font-size: 14px;
        background: rgba(0, 0, 0, 0.4);
        padding: 8px 12px;
        border-radius: 8px;
        z-index: 10;
      }
      .controls {
        position: absolute;
        bottom: 15px;
        left: 15px;
        background: rgba(255, 255, 255, 0.1);
        border: 1px solid rgba(255, 255, 255, 0.2);
        padding: 8px;
        border-radius: 8px;
        z-index: 10;
      }
      input[type="file"],
      button {
        color: white;
        background: rgba(255, 255, 255, 0.1);
        border: none;
        padding: 6px 10px;
        margin-top: 5px;
        border-radius: 5px;
        cursor: pointer;
      }
      button:hover {
        background: rgba(255, 255, 255, 0.2);
      }
    </style>
  </head>
  <body>

    <div class="controls">
      <input type="file" id="fileInput" accept=".mp3,audio/*" />
      <br />
      <button id="playButton" style="display:none;">▶ Reproducir</button>
    </div>
  </body>
</html>
```
>Sketch
```js
// --- 1. Declaraciones de Variables Globales ---
let audio, fft;
let particles = [];
let boids = [];
let flow;
let chaosIntensity = 1; // 1 = Orden, 2 = Caos
let flowScale = 2;
let modeChaos = false;
let playButton;
let boidSpeed = 1;
let audioContextStarted = false; 

// --- 2. Definición de Clases ---

class Particle {
  constructor(x, y, isExplosion = false) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(isExplosion ? random(2, 5) : random(0.5, 1.5));
    this.acc = createVector();
    this.life = random(150, 400); 
    this.h = random(0, 360); 
    this.size = random(4, 10);
    // Diversidad de formas (1: Círculo, 2: Cuadrado, 3: Diamante)
    this.type = floor(random(1, 4)); 
  }

  applyForce(f) {
    this.acc.add(f.mult(0.5 * chaosIntensity));
  }

  update(bassNorm, highNorm) {
    let gravity = createVector(0, map(chaosIntensity, 1, 2, 0.05, 0));
    this.applyForce(gravity);

    this.vel.add(this.acc);
    this.vel.limit(4 * chaosIntensity); 
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.life--;

    this.h = (this.h + highNorm * 5 * chaosIntensity) % 360;
    this.angle = atan2(this.vel.y, this.vel.x);
    
    if (this.pos.x < 0) this.pos.x = width;
    if (this.pos.x > width) this.pos.x = 0;
    if (this.pos.y < 0) this.pos.y = height;
    if (this.pos.y > height) this.pos.y = 0;
  }

  display() {
    push();
    translate(this.pos.x, this.pos.y);
    rotate(this.angle);

    // Paleta de color (brillo y oscuridad)
    let sat = map(chaosIntensity, 1, 2, 50, 100); 
    let bright = map(this.life, 0, 400, 10, 90); 
    let finalBrightness = modeChaos ? bright : 90 - (bright / 4);

    fill(this.h, sat, finalBrightness, this.life / 400); 
    noStroke();

    // Dibujar formas variadas
    if (this.type === 1 || !modeChaos) {
      ellipse(0, 0, this.size, this.size); // Círculo (Orden)
    } else if (this.type === 2) {
      rect(0, 0, this.size, this.size); // Cuadrado
    } else { // this.type === 3 o modo Caos
      beginShape(); // Diamante (Caos)
      vertex(0, -this.size);
      vertex(this.size * 0.5, 0);
      vertex(0, this.size);
      vertex(-this.size * 0.5, 0);
      endShape(CLOSE);
    }

    pop();
  }

  isDead() {
    return this.life < 0;
  }
}

class FlowField {
  constructor(res) {
    this.res = res;
    this.cols = ceil(width / res);
    this.rows = ceil(height / res);
    this.field = new Array(this.cols * this.rows);
    this.zoff = 0;
  }

  update(z, scaleFactor = 2, bassNorm = 0) {
    this.zoff = z;
    let xoff = 0;
    for (let x = 0; x < this.cols; x++) {
      let yoff = 0;
      for (let y = 0; y < this.rows; y++) {
        let angle =
          noise(xoff * scaleFactor, yoff * scaleFactor, this.zoff + bassNorm * 0.5) *
          TWO_PI *
          map(chaosIntensity, 1, 2, 1, 3); 
          
        let v = p5.Vector.fromAngle(angle);
        let index = x + y * this.cols;
        this.field[index] = v;
        yoff += 0.1;
      }
      xoff += 0.1;
    }
  }

  lookup(pos) {
    let col = constrain(floor(pos.x / this.res), 0, this.cols - 1);
    let row = constrain(floor(pos.y / this.res), 0, this.rows - 1);
    let index = col + row * this.cols;
    return this.field[index].copy();
  }
}

class Boid {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(0.5, 1.5));
    this.acc = createVector();
    this.size = random(10, 18); 
    this.maxForce = 0.2;
    this.maxSpeed = 3;
  }

  applyFlow(flow) {
    let f = flow.lookup(this.pos);
    f.mult(0.5 * chaosIntensity); 
    this.applyForce(f);
  }

  applyForce(f) {
    this.acc.add(f);
  }

  update() {
    this.vel.add(this.acc);
    this.vel.limit(this.maxSpeed * boidSpeed); 
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.edges();
  }

  display() {
    // Representan el "orden" (magenta)
    fill(300, 100, 80, 0.9); 
    noStroke();
    push();
    translate(this.pos.x, this.pos.y);
    rotate(this.vel.heading() + HALF_PI);
    triangle(0, -this.size, -this.size / 2, this.size / 2, this.size / 2, this.size / 2);
    pop();
  }

  edges() {
    if (this.pos.x < 0) this.pos.x = width;
    if (this.pos.x > width) this.pos.x = 0;
    if (this.pos.y < 0) this.pos.y = height;
    if (this.pos.y > height) this.pos.y = 0;
  }

  flock(boids) {
    if (!modeChaos) { 
      // Lógica de flocking (separación, alineación, cohesión)
    }
  }
}


// --- 3. p5.js Funciones principales ---

function setup() {
  createCanvas(windowWidth, windowHeight);
  angleMode(RADIANS); 
  colorMode(HSB, 360, 100, 100, 1);

  fft = new p5.FFT(0.8, 256);
  flow = new FlowField(40); 
  
  for (let i = 0; i < 200; i++) {
    particles.push(new Particle(random(width), random(height)));
  }
  for (let i = 0; i < 30; i++) {
    boids.push(new Boid(random(width), random(height)));
  }

  const fileInput = document.getElementById("fileInput");
  playButton = document.getElementById("playButton");

  fileInput.addEventListener("change", handleFile);
  playButton.addEventListener("click", toggleAudio);
}

function handleFile(event) {
  const file = event.target.files[0];
  if (file && file.type.includes("audio")) {
    if (audio && audio.isPlaying()) audio.stop();
    if (audio) audio.disconnect();

    audio = loadSound(URL.createObjectURL(file), 
      () => {
        fft.setInput(audio);
        playButton.style.display = "inline-block";
      }, 
      (error) => {
        console.error("Error al cargar el audio:", error);
      }
    );
  }
}

function toggleAudio() {
  if (!audio) return; 

  if (!audioContextStarted) {
    userStartAudio(); 
    audioContextStarted = true;
  }

  if (audio.isPlaying()) {
    audio.pause();
    playButton.textContent = "▶ Reproducir";
  } else {
    audio.loop(); 
    playButton.textContent = "⏸ Pausar";
  }
}

function draw() {
  // Efecto de rastro
  let bgAlpha = map(chaosIntensity, 1, 2, 0.05, 0.3);
  background(0, bgAlpha);

  if (audio && audio.isPlaying()) {
    let spectrum = fft.analyze();
    let bass = fft.getEnergy("bass");
    let high = fft.getEnergy("treble");

    let bassNorm = map(bass, 0, 255, 0, 1);
    let highNorm = map(high, 0, 255, 0, 1);
    
    chaosIntensity = modeChaos ? 2 : 1; 

    flow.update(frameCount * 0.002 * chaosIntensity, flowScale, bassNorm);

    // --- Aplicar fuerza de "Imán" del ratón ---
    if (mouseIsPressed) {
      let mousePos = createVector(mouseX, mouseY);
      for (let p of particles) {
        let dir = p5.Vector.sub(mousePos, p.pos);
        let dist = dir.mag();
        
        if (dist < 150) {
          dir.normalize();
          // Fuerza de atracción más fuerte cuanto más cerca
          dir.mult(map(dist, 0, 150, 0.5, 0)); 
          p.applyForce(dir.mult(chaosIntensity * 2)); 
        }
      }
    }
    
    // --- Modo de mezcla aditivo para el brillo (Dulces) ---
    blendMode(ADD); 

    // Partículas (Dulces Caóticos)
    for (let i = particles.length - 1; i >= 0; i--) {
      let p = particles[i];
      let f = flow.lookup(p.pos);
      p.applyForce(f);
      p.update(bassNorm, highNorm);
      p.display();
      if (p.isDead()) particles.splice(i, 1);
    }

    // --- Volver al modo de mezcla normal ---
    blendMode(BLEND); 

    // Boids (Criaturas/Orden)
    for (let b of boids) {
      b.applyFlow(flow);
      b.flock(boids); 
      b.update();
      b.display();
    }

    // --- Post-Procesamiento (Filtros de Glitch/Distorsión) ---
    if (modeChaos) {
      // Desenfoque leve en caos basado en el ritmo alto
      filter(BLUR, map(high, 0, 255, 0.5, 2)); 
      
      // Flash de color invertido ocasional (Glitch)
      if (frameCount % 100 < 5) {
         filter(INVERT); 
      }
    }
  } else {
    fill(255, 50);
    textAlign(CENTER, CENTER);
    textSize(20);
    text("Sube una canción MP3 y presiona ▶", width / 2, height / 2);
  }
  
  displayControls();
}

function displayControls() {
  fill(255, 100);
  textAlign(LEFT, TOP);
  textSize(12);
  
  // Indicador de modo
  let modeText = modeChaos ? "Modo: CAOS 💥 (Alta energía)" : "Modo: ORDEN ✨ (Control/Reglas)";
  fill(modeChaos ? color(0, 80, 100) : color(240, 80, 100)); 
  noStroke();
  rect(10, 50, 220, 25, 5);
  fill(0);
  text(modeText, 15, 55);
  
  fill(255, 100);
  text("Teclas ⬆/⬇: Alternar Caos/Orden", 10, 85);
  text("Tecla 'A': Crear Partículas (Random)", 10, 100);
  text("Mouse (Click): Explosión de Dulces", 10, 115);
}


// --- 4. Interacción ---

function mousePressed() {
  // Input: Mouse (Explosión)
  for (let i = 0; i < 30; i++) {
    let p = new Particle(mouseX, mouseY, true);
    particles.push(p);
  }
}

function keyPressed() {
  // Input: Teclado (Flechas: Caos/Orden)
  if (keyCode === UP_ARROW) {
    modeChaos = true; 
    chaosIntensity = 2;
    boidSpeed = 0.5; 
  } else if (keyCode === DOWN_ARROW) {
    modeChaos = false; 
    chaosIntensity = 1;
    boidSpeed = 1.5; 
  }
  
  // Tecla 'A': Creación de Partículas en lugares random
  if (key === 'a' || key === 'A') {
    for (let i = 0; i < 15; i++) {
      particles.push(new Particle(random(width), random(height)));
    }
  }
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
  flow = new FlowField(40); 
}
```
2. Un enlace a tu sketch en el editor de p5.js.
> [https://editor.p5js.org/vlr1004/sketches/KOJwGeSFo](https://editor.p5js.org/vlr1004/sketches/q0rjRAT6y)
3. Capturas de pantalla mostrando tu pieza en acción.


# Evidencias
## Nota propuesta


## Defensa nota







