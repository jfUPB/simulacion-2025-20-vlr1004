# Evidencias de la unidad 6
# Actividad 1
- Imagenes que me gustaron:  
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/acd57fe1-e2fe-4c88-894a-ff8dc65ac0b0" /> <img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/77407424-dd98-4088-86dc-a4162f0f87ee" />
- ¿Qué me inspira su trabajo?
  > Me gusta mucho las formas creadas, sus obras parecen pinceladas, texturas y patrones. Cuando utiliza color me recuerda al impresionismo, una rama del arte que en lo personal me gusta mucho.

# Actividad 2

1. ¿Qué es una fuerza de dirección (steering force)?
> Es una fuerza de dirección, con esta ecuación: Fuerza de dirección=Velocidad Deseada−Velocidad Actual. Guia el movimiento con su aceleración hacia un objeto deseado.  
2. ¿Qué diferencia tiene este tipo de fuerza con las que ya hemos estudiado en el contexto de la simulación de agentes?
> Diferenciada porque los calculos de las fuerzas es realizada por el propio agente. No representa una interacción física directa, sino una estrategia para mover al agente de forma “creíble” o “intencional”.
3. ¿Qué relación tiene la steering force con Craig Reynolds y su trabajo en simulación de comportamiento animal?
> El es pionero en el uso de esta fuerza, el creó el modelo de Boids; lo que ayudo a que la idea de steering force se convirtió en la base de cómo simulamos movimientos colectivos en IA, animación, videojuegos y robótica.

# Actividad 3
1. Explica brevemente la estructura de datos usada para el campo de flujo y cómo se generan sus vectores.
> **Estructura:** Todo esta organizado como una cuadricula (una matriz), "`esolution` es lo que calcula el tamaño de la cuadricula, y de ella se calcula `cols = width / resolution` y `rows = height / resolution`. Se recorren las celdas con dos bucles (i, j) y se incrementan los offsets `xoff`, `yoff` para obtener una variación suave entre celdas.  
**Generación de vectores:** La implementación típica usa Perlin noise para calcular un ángulo en cada celda y luego crea un vector a partir de ese ángulo.

2. Describe con tus palabras cómo un agente utiliza el campo para calcular su fuerza de dirección.
> El agente pregunta al `FlowField` por el vector locar en su posición y devuelve una copia del vector de esa celda. El vector local es la velocidad deseada lo que es la dirección del capmo que se quiere que siga. Se calcula la fuerza de dirección utilizando la formula `steer = desired - velocity` para luego limitarse esa fuerza para que el agente no gire/acelere de maneras extrañas.

3. Lista los parámetros clave identificados (resolución, maxspeed, maxforce).
>resolution — tamaño de celda del flowfield; define cuán “granular” es el campo. Celdas grandes → direcciones constantes en áreas amplias; celdas pequeñas → mucha variación local. (se usa en cols = width / resolution, rows = height / resolution). 
Nature of Code

> cols, rows — número de celdas en x e y, derivadas de resolution. 
Nature of Code

> noise step (xoff, yoff) — el incremento que aplicas al recorrer el ruido (ej. 0.1); controla la suavidad espacial del campo (valores pequeños → campo más suave). 
Nature of Code

> maxspeed — velocidad máxima a la que el agente quiere desplazarse (se usa con desired.setMag(this.maxspeed)). Afecta la rapidez del agente. (ejemplo en el libro usa valores como 8 en ejemplos concretos, pero en cada sketch se ajusta según intención). 
Nature of Code

> maxforce — tope de la magnitud de la steering force (steer.limit(this.maxforce)); controla cuán maniobrable/ágil es el agente (valores bajos → giros lentos). 
Nature of Code

> número de agentes y tamaño visual r pueden influir en la percepción de comportamiento colectivo (más agentes = más densidad / posible agrupamiento). 
Nature of Code

4. Describe la modificación que realizaste al código y explica detalladamente el efecto que tuvo en el movimiento y comportamiento colectivo de los agentes. Incluye una captura de pantalla o GIF si ilustra bien el cambio. Muestra el fragmento de código modificado.
> Al reducir el radio r de 6 a 0.6 los agentes se dibujan mucho más pequeños, por lo que visualmente parecen partículas diminutas y se perciben más numerosos o “finos”. Al mismo tiempo, al aumentar maxforce de un valor bajo (por ejemplo 0.1–0.3) a 6, cada agente puede cambiar su aceleración casi instantáneamente: gira mucho más brusco, sigue el campo de flujo con movimientos rápidos y angulosos y responde de forma extremadamente sensible a las variaciones del campo. En conjunto, el resultado es un movimiento más caótico y reactivo, con trayectorias menos suaves y con agentes que parecen “vibrar” o zigzaguear en lugar de deslizarse lentamente.
> <img width="763" height="285" alt="image" src="https://github.com/user-attachments/assets/b1c700c4-f83c-4692-a46a-7bb1786018fa" />
```js
class Vehicle {
constructor(x, y, ms, mf) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(0, 0);
    this.r = 0.6;
    this.maxspeed = 4;
    this.maxforce = 6;
  }

 ```


# Actividad 4
1. Explica con tus palabras el objetivo y la lógica general de cálculo de cada una de las tres reglas de Flocking (Separación, Alineación, Cohesión).
> Separation  
Objetivo: evitar el hacinamiento; que cada boid no choque ni quede pegado a vecinos muy cercanos.  
Lógica de cálculo: para cada vecino demasiado cercano, el boid calcula un vector que apunta hacia afuera (de sí mismo hacia la posición opuesta del vecino), lo pondera (por ejemplo inversamente proporcional a la distancia) y suma/normaliza esas contribuciones. El resultado es una fuerza que empuja al boid lejos de los vecinos próximos.

> Alignment  
Objetivo: alinear la dirección/velocidad del boid con la del grupo para que se muevan en la misma dirección.  
Lógica de cálculo: el boid promedia las velocidades de sus vecinos dentro de su radio de percepción y crea una velocidad deseada igual a ese promedio (normalizada a la maxspeed). La steering force es la diferencia entre esa velocidad deseada y la velocidad actual.

> Cohesion  
Objetivo: mantener unida la banda; mover al boid hacia el “centro de masa” local de sus vecinos.  
Lógica de cálculo: el boid promedia las posiciones de sus vecinos (centro local) y calcula un vector seek que lo lleva hacia ese punto medio. Ese seek se convierte en la fuerza de cohesión (otra steering force).

2. Lista los parámetros clave identificados (radio de percepción, pesos de las reglas, maxspeed, maxforce).

> perceptionRadius (o neighbourDist) — radio de percepción; define cuáles otros boids se consideran “vecinos”. Controla el alcance de separación/alineación/cohesión. (valores típicos: 25–80 px).

> desiredSeparation — distancia mínima deseada para la separación (ej. 20–30 px).

> separationWeight, alignmentWeight, cohesionWeight — multiplicadores que ponderan la influencia de cada regla cuando se combinan. Si uno es alto dominará la dinámica.

> maxspeed — velocidad máxima del boid (controla ritmo general).

> maxforce — magnitud máxima de la steering force (controla la maniobrabilidad; valores pequeños → giros suaves; valores grandes → giros bruscos).

3. Describe la modificación que realizaste al código y explica detalladamente el efecto que tuvo en el comportamiento colectivo del enjambre (¿Se dispersan? ¿Forman grupos compactos? ¿se mueven caóticamente?). Incluye una captura de pantalla o GIF si ilustra bien el cambio. Muestra el fragmento de código modificado.

> Los boids ahora son más pequeños y avanzan más lentamente, lo que da una sensación de bandada más “lenta y fina”. Al aumentar el radio de alineación tienden a orientarse con muchos más vecinos, por lo que se mueven más en direcciones parecidas, pero al reducir el radio de cohesión no se acercan tanto a un centro común. El resultado es que las bandadas son más sueltas y extendidas, se mueven de forma ordenada (alineadas) pero menos compactas y con menos tendencia a formar grupos muy apretados.
> <img width="602" height="283" alt="image" src="https://github.com/user-attachments/assets/4238f85f-e3ae-45b0-8bbc-07d502f9bde7" />
 ```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

// Boid class
// Methods for Separation, Cohesion, Alignment added

class Boid {
  constructor(x, y) {
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 1));
    this.position = createVector(x, y);
    this.r = 2;
    this.maxspeed = 1; // Maximum speed
    this.maxforce = 0.05; // Maximum steering force
  }

  run(boids) {
    this.flock(boids);
    this.update();
    this.borders();
    this.show();
  }

  applyForce(force) {
    // We could add mass here if we want A = F / M
    this.acceleration.add(force);
  }

  // We accumulate a new acceleration each time based on three rules
  flock(boids) {
    let sep = this.separate(boids); // Separation
    let ali = this.align(boids); // Alignment
    let coh = this.cohere(boids); // Cohesion
    // Arbitrarily weight these forces
    sep.mult(1.5);
    ali.mult(1.0);
    coh.mult(1.0);
    // Add the force vectors to acceleration
    this.applyForce(sep);
    this.applyForce(ali);
    this.applyForce(coh);
  }

  // Method to update location
  update() {
    // Update velocity
    this.velocity.add(this.acceleration);
    // Limit speed
    this.velocity.limit(this.maxspeed);
    this.position.add(this.velocity);
    // Reset accelertion to 0 each cycle
    this.acceleration.mult(0);
  }

  // A method that calculates and applies a steering force towards a target
  // STEER = DESIRED MINUS VELOCITY
  seek(target) {
    let desired = p5.Vector.sub(target, this.position); // A vector pointing from the location to the target
    // Normalize desired and scale to maximum speed
    desired.normalize();
    desired.mult(this.maxspeed);
    // Steering = Desired minus Velocity
    let steer = p5.Vector.sub(desired, this.velocity);
    steer.limit(this.maxforce); // Limit to maximum steering force
    return steer;
  }

  show() {
    // Draw a triangle rotated in the direction of velocity
    let angle = this.velocity.heading();
    fill(127);
    stroke(0);
    push();
    translate(this.position.x, this.position.y);
    rotate(angle);
    beginShape();
    vertex(this.r * 4, 0);
    vertex(-this.r * 2, -this.r);
    vertex(-this.r * 2, this.r);
    endShape(CLOSE);
    pop();
  }

  // Wraparound
  borders() {
    if (this.position.x < -this.r) this.position.x = width + this.r;
    if (this.position.y < -this.r) this.position.y = height + this.r;
    if (this.position.x > width + this.r) this.position.x = -this.r;
    if (this.position.y > height + this.r) this.position.y = -this.r;
  }

  // Separation
  // Method checks for nearby boids and steers away
  separate(boids) {
    let desiredSeparation = 25;
    let steer = createVector(0, 0);
    let count = 0;
    // For every boid in the system, check if it's too close
    for (let i = 0; i < boids.length; i++) {
      let d = p5.Vector.dist(this.position, boids[i].position);
      // If the distance is greater than 0 and less than an arbitrary amount (0 when you are yourself)
      if (d > 0 && d < desiredSeparation) {
        // Calculate vector pointing away from neighbor
        let diff = p5.Vector.sub(this.position, boids[i].position);
        diff.normalize();
        diff.div(d); // Weight by distance
        steer.add(diff);
        count++; // Keep track of how many
      }
    }
    // Average -- divide by how many
    if (count > 0) {
      steer.div(count);
    }

    // As long as the vector is greater than 0
    if (steer.mag() > 0) {
      // Implement Reynolds: Steering = Desired - Velocity
      steer.normalize();
      steer.mult(this.maxspeed);
      steer.sub(this.velocity);
      steer.limit(this.maxforce);
    }
    return steer;
  }

  // Alignment
  // For every nearby boid in the system, calculate the average velocity
  align(boids) {
    let neighborDistance = 65;
    let sum = createVector(0, 0);
    let count = 0;
    for (let i = 0; i < boids.length; i++) {
      let d = p5.Vector.dist(this.position, boids[i].position);
      if (d > 0 && d < neighborDistance) {
        sum.add(boids[i].velocity);
        count++;
      }
    }
    if (count > 0) {
      sum.div(count);
      sum.normalize();
      sum.mult(this.maxspeed);
      let steer = p5.Vector.sub(sum, this.velocity);
      steer.limit(this.maxforce);
      return steer;
    } else {
      return createVector(0, 0);
    }
  }

  // Cohesion
  // For the average location (i.e. center) of all nearby boids, calculate steering vector towards that location
  cohere(boids) {
    let neighborDistance = 30;
    let sum = createVector(0, 0); // Start with empty vector to accumulate all locations
    let count = 0;
    for (let i = 0; i < boids.length; i++) {
      let d = p5.Vector.dist(this.position, boids[i].position);
      if (d > 0 && d < neighborDistance) {
        sum.add(boids[i].position); // Add location
        count++;
      }
    }
    if (count > 0) {
      sum.div(count);
      return this.seek(sum); // Steer towards the location
    } else {
      return createVector(0, 0);
    }
  }
}


 ```


# Apply: Actividad 5
1. Documenta todo el proceso de diseño y creación en tu bitácora, incluyendo bocetos y decisiones de diseño.  
  a. Elige un tema musical que te inspire.  
  b. Diseña una pieza de arte generativo que utilice el algoritmo de flow fields y/o flocking.  
  c. Vas a visualizar el tema musical y además vas a “tocar” las visuales, es decir, tu pieza de arte debe ser interactiva y debe permitir la interpretación en tiempo real de las visuales como si fuera un instrumento más que acompaña de manera coherente el tema musical.
> Voy a elegir la canción de Milo J "Gil". Teniendo en cuenta que la obra generativa parte de ser un instrumento más que acompaña de manera coherente el tema musical, va ser un cambio visual donde se podra subir la canción. Los boids se mueven y reaccionan al nivel de audio. La interactividad corresponde al uso del mouse que cambia el color y grosor dependiendo de como se mueva, y las letras "WASD" que frenan/aceleran y rotan el enjambre.
2. El código fuente completo de tu sketch en p5.js.
> Sketch.js 
 ```js
let flock;
let song;
let amp;

function setup() {
  createCanvas(windowWidth, windowHeight);
  colorMode(HSB, 255);

  flock = new Flock();
  for (let i = 0; i < 150; i++) {
    flock.addBoid(new Boid(random(width), random(height)));
  }

  // input para cargar audio
  let fileInput = createFileInput(handleFile);
  fileInput.position(10, 10);

  amp = new p5.Amplitude();
}

function handleFile(file) {
  if (file.type === 'audio') {
    if (song) {
      song.stop();
    }
    song = loadSound(file.data, () => {
      song.loop(); // reproduce automáticamente
    });
  }
}

function draw() {
  background(0, 40); // cola para efecto
  flock.run();

  // nivel de audio (beat)
  let level = amp.getLevel();
  let size = map(level, 0, 0.3, 2, 20); // tamaño reacciona al beat

  let hueValue = map(mouseY, 0, height, 0, 255); // color con mouse
  let weight = map(mouseX, 0, width, 1, 10);     // grosor con mouse

  // dibuja cada boid como punto “instrumento”
  for (let b of flock.boids) {
    stroke(hueValue, 255, 255);
    strokeWeight(size + weight);
    point(b.position.x, b.position.y);
  }

  // controles WASD como “instrumento”:
  if (keyIsDown(65)) { // A - rota izquierda
    for (let b of flock.boids) b.velocity.rotate(-0.05);
  }
  if (keyIsDown(68)) { // D - rota derecha
    for (let b of flock.boids) b.velocity.rotate(0.05);
  }
  if (keyIsDown(87)) { // W - acelera “sube intensidad”
    for (let b of flock.boids) b.maxspeed = 7;
  }
  if (keyIsDown(83)) { // S - frena “baja intensidad”
    for (let b of flock.boids) b.maxspeed = 2;
  }
}

 ```
> Folck
 ```js
class Flock {
  constructor() {
    this.boids = [];
  }

  addBoid(b) {
    this.boids.push(b);
  }

  run() {
    for (let boid of this.boids) {
      boid.edges();
      boid.flock(this.boids);
      boid.update();
      boid.show();
    }
  }
}

 ```
> Boild
 ```js
class Boid {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.velocity = p5.Vector.random2D();
    this.velocity.setMag(random(2, 4));
    this.acceleration = createVector(0, 0);
    this.maxForce = 0.3;
    this.maxSpeed = 4;
  }

  edges() {
    if (this.position.x > width) this.position.x = 0;
    if (this.position.x < 0) this.position.x = width;
    if (this.position.y > height) this.position.y = 0;
    if (this.position.y < 0) this.position.y = height;
  }

  align(boids) {
    let perceptionRadius = 50;
    let steering = createVector();
    let total = 0;
    for (let other of boids) {
      let d = dist(
        this.position.x,
        this.position.y,
        other.position.x,
        other.position.y
      );
      if (other != this && d < perceptionRadius) {
        steering.add(other.velocity);
        total++;
      }
    }
    if (total > 0) {
      steering.div(total);
      steering.setMag(this.maxSpeed);
      steering.sub(this.velocity);
      steering.limit(this.maxForce);
    }
    return steering;
  }

  cohesion(boids) {
    let perceptionRadius = 50;
    let steering = createVector();
    let total = 0;
    for (let other of boids) {
      let d = dist(
        this.position.x,
        this.position.y,
        other.position.x,
        other.position.y
      );
      if (other != this && d < perceptionRadius) {
        steering.add(other.position);
        total++;
      }
    }
    if (total > 0) {
      steering.div(total);
      steering.sub(this.position);
      steering.setMag(this.maxSpeed);
      steering.sub(this.velocity);
      steering.limit(this.maxForce);
    }
    return steering;
  }

  separation(boids) {
    let perceptionRadius = 30;
    let steering = createVector();
    let total = 0;
    for (let other of boids) {
      let d = dist(
        this.position.x,
        this.position.y,
        other.position.x,
        other.position.y
      );
      if (other != this && d < perceptionRadius) {
        let diff = p5.Vector.sub(this.position, other.position);
        diff.div(d * d);
        steering.add(diff);
        total++;
      }
    }
    if (total > 0) {
      steering.div(total);
      steering.setMag(this.maxSpeed);
      steering.sub(this.velocity);
      steering.limit(this.maxForce);
    }
    return steering;
  }

  flock(boids) {
    let alignment = this.align(boids);
    let cohesion = this.cohesion(boids);
    let separation = this.separation(boids);

    // pesos: aquí puedes experimentar
    alignment.mult(1.0);
    cohesion.mult(0.8);
    separation.mult(1.5);

    this.acceleration.add(alignment);
    this.acceleration.add(cohesion);
    this.acceleration.add(separation);
  }

  update() {
    this.position.add(this.velocity);
    this.velocity.add(this.acceleration);
    this.velocity.limit(this.maxSpeed);
    this.acceleration.mult(0);
  }

  show() {
    strokeWeight(6);
    stroke(255, 100, 100, 200);
    point(this.position.x, this.position.y);
  }
}

 ```
3. Un enlace a tu sketch en el editor de p5.js.
> https://editor.p5js.org/vlr1004/sketches/4FZNLIhwz
4. Capturas de pantalla mostrando tu pieza en acción.
> <img width="842" height="449" alt="image" src="https://github.com/user-attachments/assets/daf95743-fdf9-486f-8a05-e5f9ed73e93d" />


# Autoevaluación
> Durante el desarrollo de todas las actividades propuestas logré cumplir con cada uno de los objetivos y entregables solicitados por el profesor, integrando no solo el contenido mínimo, sino también exploraciones adicionales para enriquecer el resultado. Por estas razones considero que mi desempeño corresponde a la calificación máxima **(5.0)**.

> Actividad 1 – Fuerzas de dirección (Steering Forces)
Expliqué con claridad el concepto de fuerza de dirección, la diferencié de las fuerzas tradicionales vistas en clase y la relacioné con el trabajo de Craig Reynolds. La respuesta es completa, precisa y conectada con ejemplos prácticos de simulación de agentes.

> Actividad 2 – Campo de flujo (Flow Field)  
Analicé el código del ejemplo del Flow Field, identifiqué la estructura de datos utilizada y describí cómo se generan los vectores. Además, expliqué cómo un agente usa el campo para calcular su fuerza de dirección e identifiqué correctamente los parámetros clave. Realicé modificaciones al código y documenté con capturas y fragmentos de código los efectos obtenidos.

> Actividad 3 – Modificación de parámetros  
Apliqué cambios a los valores de maxspeed y maxforce en los vehículos, describiendo en mi bitácora el impacto de dichos cambios en el comportamiento de los agentes. Esto demuestra comprensión del modelo y experimentación controlada.

> Actividad 4 – Flocking (Separación, Alineación y Cohesión)  
Ejecuté y estudié el ejemplo de flocking de “The Nature of Code”. Identifiqué las tres reglas fundamentales y las expliqué con mis propias palabras, detallando la lógica de cálculo de cada una. Ubique los parámetros clave (radio de percepción, pesos de las reglas, maxspeed, maxforce) y realicé modificaciones drásticas en estos valores, documentando con capturas cómo cambia el comportamiento colectivo (dispersión, formación de grupos, movimientos caóticos).

>Actividad 5 – Arte generativo interactivo  
Diseñé una pieza de arte generativo basada en flocking y flow fields inspirada en la canción “Gil” de Milo J. Implementé la carga de un archivo MP3 desde mi computador para que se reproduzca automáticamente y utilicé la amplitud de sonido para sincronizar las visuales con el beat de la música. Añadí controles interactivos con teclado y mouse para que la pieza se pueda “tocar” en tiempo real como un instrumento visual, cumpliendo exactamente con lo solicitado por el profesor. Documenté el proceso con código completo, capturas y explicación de decisiones de diseño.



