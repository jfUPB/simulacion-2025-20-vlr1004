# Evidencias de la unidad 4

## Explicación conceptual de la obra

* ¿Qué concepto de la unidad 4 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> El concepto de la unidad 4 es Motion 101, lo apliqué en el sistema de partículas orbitantes, que cambian de estado entre orbiting, escaping y returning. Cada estado se comporta con reglas distintas, pero siempre bajo la lógica de Motion 101, ya que las partículas actualizan su posición con base en la velocidad y la aceleración que reciben.

* ¿Qué concepto de la unidad 3 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> El concepto de la unidad 3 es la oscilación y el movimiento angular. Lo implementé en el péndulo principal, que utiliza un ángulo, una velocidad angular y una aceleración angular para simular el movimiento oscilatorio. También se refleja en las partículas que orbitan alrededor de los círculos principales siguiendo trayectorias circulares.

* ¿Qué concepto de la unidad 2 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> El concepto de la unidad 2 es el uso de fuerzas. Lo apliqué en la simulación de la gravedad en el péndulo principal y también en las partículas que retornan a su órbita, ya que se les aplica una fuerza de atracción que las dirige hacia el centro hasta que se reintegran a la trayectoria circular.

* ¿Qué concepto de la unidad 1 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> El concepto de la unidad 1 son los vectores. Los utilicé en las partículas que escapan y retornan, ya que su movimiento está definido por vectores de velocidad generados con ``` p5.Vector.random2D()```. Además, para el retorno se calcula un vector de dirección hacia el punto de anclaje, que luego se normaliza y escala para simular la fuerza de atracción.

## ¿Cómo resolviste la interacción?
> Tu respuesta aquí:
> Lo resolvi con el teclado:
> Con las flechas izquierda y derecha modificas la velocidad angular del péndulo, afectando su movimiento.
> Con la tecla M agregas nuevas partículas orbitantes, que aparecen con colores y posiciones iniciales aleatorias.
> Con las flechas arriba y abajo seleccionas un grupo de partículas para que entren en estado de escape, cambiando su trayectoria y comportamiento temporalmente antes de regresar a orbitar.

## Enlace a la obra en el editor de p5.js

[[Aquí está mi obra](https://editor.p5js.org/vlr1004/sketches/P3QaJN4I5)](URL)


## Código de la obra 

``` js
// Inspirado en The Nature of Code (Shiffman)

let angle = 0;
let aVelocity = 0;
let aAcceleration = 0;
let r = 100;    // radio (distancia desde el centro a los círculos)
let damping = 0.995;
let t = 0; // para Perlin noise

let orbitingParticles = []; // lista de partículas orbitantes

function setup() {
  createCanvas(800, 600);
  background(255);
}

function draw() {
  // Desvanecer un poco el fondo para ver estelas
  noStroke();
  fill(255, 10);
  rect(0, 0, width, height);

  translate(width / 2, height / 2);
  
  // --- Física tipo péndulo ---
  let gravity = 0.01; 
  aAcceleration = (-gravity / r) * sin(angle);
  aVelocity += aAcceleration;
  angle += aVelocity;
  aVelocity *= damping;
  
  // --- Posiciones de los círculos principales ---
  let x1 = r * cos(angle);
  let y1 = r * sin(angle);
  let x2 = -r * cos(angle);
  let y2 = -r * sin(angle);

  // --- Color dinámico con Perlin noise ---
  let col1 = color(noise(t) * 255, noise(t + 50) * 255, noise(t + 100) * 255, 150);
  let col2 = color(noise(t + 200) * 255, noise(t + 250) * 255, noise(t + 300) * 255, 150);
  
  // Rastro de las partículas principales
  fill(col1);
  noStroke();
  circle(x1, y1, 10); 
  
  fill(col2);
  noStroke();
  circle(x2, y2, 10); 

  // círculos principales
  fill(127);
  stroke(0);
  circle(x1, y1, 16);
  circle(x2, y2, 16);
  
  // --- Actualizar y dibujar partículas orbitantes ---
  for (let p of orbitingParticles) {
    p.update(x1, y1, x2, y2); // pasar posiciones de los principales
    p.display();
  }

  t += 0.01;
}

// Clase para partículas orbitantes
class OrbitingParticle {
  constructor(anchorIndex) {
    this.anchorIndex = anchorIndex; // 0 = orbita x1,y1 | 1 = orbita x2,y2
    this.orbitR = random(20, 60); // radio de órbita
    this.angle = random(TWO_PI);
    this.aVel = random(0.01, 0.05); // velocidad angular
    this.col = color(random(255), random(255), random(255), 200);
    this.x = 0;
    this.y = 0;
    
    // --- NUEVAS PROPIEDADES ---
    this.state = 'orbiting'; // 'orbiting' o 'escaping'
    this.escapeStartTime = 0;
    this.escapeDuration = 5000; // 5 segundos en milisegundos
    this.vel = p5.Vector.random2D().mult(random(1, 3)); // Vector de velocidad para el escape
  }
  
  update(x1, y1, x2, y2) {
    if (this.state === 'orbiting') {
      this.angle += this.aVel;
      let cx, cy;
      if (this.anchorIndex === 0) {
        cx = x1;
        cy = y1;
      } else {
        cx = x2;
        cy = y2;
      }
      this.x = cx + this.orbitR * cos(this.angle);
      this.y = cy + this.orbitR * sin(this.angle);
    } else if (this.state === 'escaping') {
      // Movimiento de escape
      this.x += this.vel.x;
      this.y += this.vel.y;
      
      // La partícula puede rebotar en los bordes para mantenerse en pantalla
      if (this.x > width/2 || this.x < -width/2) {
        this.vel.x *= -1;
      }
      if (this.y > height/2 || this.y < -height/2) {
        this.vel.y *= -1;
      }

      // Comprobar si el tiempo de escape ha terminado
      if (millis() - this.escapeStartTime > this.escapeDuration) {
        this.state = 'returning';
      }
    } else if (this.state === 'returning') {
      let targetX, targetY;
      if (this.anchorIndex === 0) {
        targetX = x1;
        targetY = y1;
      } else {
        targetX = x2;
        targetY = y2;
      }
      
      // Vector que apunta hacia el centro de la órbita
      let returnVec = createVector(targetX - this.x, targetY - this.y);
      returnVec.normalize().mult(5); // Fuerza para volver

      this.vel.add(returnVec);
      this.vel.limit(5); // Limitar la velocidad para que no vuele demasiado rápido

      this.x += this.vel.x;
      this.y += this.vel.y;

      // Detectar si está lo suficientemente cerca del centro de la órbita
      if (dist(this.x, this.y, targetX, targetY) < 10) {
        this.state = 'orbiting';
        this.angle = atan2(this.y - targetY, this.x - targetX); // Reajustar el ángulo
      }
    }
  }
  
  display() {
    fill(this.col);
    noStroke();
    circle(this.x, this.y, 12);
  }
}

// Control con teclado: aumentar/disminuir ángulo, crear partículas y manejar el escape
function keyPressed() {
  if (keyCode === LEFT_ARROW) {
    aVelocity -= 0.05;
  } else if (keyCode === RIGHT_ARROW) {
    aVelocity += 0.05;
  } else if (key === 'm' || key === 'M') {
    // Crear nueva partícula que orbita a uno de los principales
    let anchorIndex = random([0, 1]); // 0 = x1,y1 | 1 = x2,y2
    orbitingParticles.push(new OrbitingParticle(anchorIndex));
  } else if (keyCode === UP_ARROW || keyCode === DOWN_ARROW) {
    // Seleccionar 5 partículas aleatorias para el escape
    let particlesToEscape = min(5, orbitingParticles.length);
    for (let i = 0; i < particlesToEscape; i++) {
      let p = orbitingParticles[floor(random(orbitingParticles.length))];
      // Asegurarse de que no esté ya escapando
      if (p.state === 'orbiting') {
        p.state = 'escaping';
        p.escapeStartTime = millis(); // Registrar el tiempo de inicio
        p.vel = p5.Vector.random2D().mult(random(2, 4)); // Darle una nueva velocidad aleatoria
      }
    }
  }
}
```

## Captura de pantalla representativa
<img width="844" height="644" alt="image" src="https://github.com/user-attachments/assets/e2d7f45c-fa3e-4673-ae0c-63082cf7614e" />







