# Unidad 1

## 🔎 Fase: Set + Seek

### Actividad 01
- Piensa y describe en una sola frase y en tus propias palabras cómo la aleatoriedad influye en el arte generativo:

Es una muestra unica para la pieza que se esta creando donde cada momento sera diferente al experimentar la pieza.

### Actividad 02
- Cuál es el papel de la aleatoriedad en su obra:  
    La dirección de movimiento de las lineas y los colores.
- Según tu perfil profesional, cómo se aplica el concepto de aleatoriedad en el tipo de proyectos que desarrollas. Ilustra tu respuesta con ejemplos concretos.  
    *Estética y ambientación – climas o efectos aleatorios:* Eventos inusiales para simular la realidad (que aunque existe el pronostico del tiempo sigue siendo disperso su comportamiento), juegos como Minecraft tienen aleatoreamente su estado de tiempo.
    *Animación interactiva – decisiones aleatorias:* Ciertos eventos o diálogos cambien sutilmente cada vez que se ve, haciendo que la historia parezca más viva y menos predecible. En The Binding of Isaac, puedes encontrarte con diferentes objetos que pueden ayudar o uno inutil así mismo en el juego Tunche al no tener una narrativa lineal los niveles aunque no infinitos, puede ser que nunca en una partida llegues a encontrarte a jefes especificos.


### Actividad 03
- Qué espero que suceda?  
  Va aumentar las probabilidades de ir a un lado, el color va a cambiar y el tamaño.
- Ocurrió lo que esperabas? ¿Por qué crees que sí o por qué crees que no?  
  Algo así, ya que subi la probabilidad de que subiera, pero al cambiar el numero de random, solo subia. Creo que solo parece que sube ya que las probabilidades subieron. Si se cambio los colores. No le cambie el tamaño era donde iniciaba (JAJAJAJAJ)

### Actividad 04
- En tus propias palabras cuál es la diferencia entre una distribución uniforme y una no uniforme de números aleatorios.
La no uniforme al tener una media se hace mas probable que aparezca los numeros/puntos al rededor de esa media, pero existe una probabilidad que tambien estén más alejados.
En la uniforme, existe un rango que siempre va haber un momento donde los números van a aparecer.

### Actividad 05
-Codigo:
````
let puntos = [];

function setup() {
  createCanvas(600, 400);
  background(255);
  noStroke();
}

function draw() {
  // Genera un punto con distribución normal centrado en el medio del canvas
  let x = randomGaussian(width / 2, 60); // media = mitad del canvas, desviación = 60
  let y = randomGaussian(height / 2, 60);

  puntos.push({x, y});

  // Dibujar los puntos
  fill(100, 100, 255, 50); // Azul translúcido
  ellipse(x, y, 5, 5);

  // Limita la cantidad de puntos para evitar que se ralentice
  if (puntos.length > 2000) {
    puntos.shift();
    background(255); // Limpia el fondo para evitar saturación
    for (let p of puntos) {
      fill(100, 100, 255, 50);
      ellipse(p.x, p.y, 5, 5);
    }
  }
}
````

- Link: https://editor.p5js.org/vlr1004/sketches/I3ejPxOeT
- Captura de pantalla:
<img width="1917" height="893" alt="image" src="https://github.com/user-attachments/assets/95a889cf-f22f-4f64-8436-02c45a485ee3" />

  
### Actividad 06
- Explicación: En el documento hablaban de la busqueda de comida, donde si se vuelve en un mismo lugar varias veces, la busqueda es ineficiente. Por lo que dar saltos más grandes y buscar un momento en el lugar lo hace "mas eficiente". 
- Código:

````
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
  }

  step() {
    let angle = random(TWO_PI);

    let stepSize = this.levy();
    
    this.x += cos(angle) * stepSize;
    this.y += sin(angle) * stepSize;

    this.x = constrain(this.x, 0, width);
    this.y = constrain(this.y, 0, height);
  }

  levy() {
    let r = random(1);
    return 1 / pow(r, 1.5);
  }

  show() {
    stroke(0);
    strokeWeight(2);
    point(this.x, this.y);
  }
}
````

- Enlace: https://editor.p5js.org/vlr1004/sketches/nBAbZUsUB
- Captura:
<img width="1919" height="922" alt="image" src="https://github.com/user-attachments/assets/9437fe15-bb6a-4b3e-8ee2-78b483affe7d" />

  
### Actividad 07
- Explica el concepto qué resultados esberabas obtener: El concepto de Perlin noise, toma el concepto de el movimiento organico de elementos de la naturaleza, bajandole la rudez y la rapidez. Por lo que pensé en el movimiento de sacar lana del ovillo (dato innecesario, realmente fue pensando en la naturaleza de la finca, recorde a mi abuela paterna tejiendo). Pense que iba a ser movimientos circulares pero al toparse con los limites del canva, se colocaba en linea como si fuera una pared, entonces pensé que falle pero lo contrario, el movimiento aunque no rigido de la lana si hubiera chocado contra una pared, si hubiera creado cierto tipo de linea. 
- Código:
````
let t = 0;         // Tiempo para el ruido
let x, y;          // Posición actual
let path = [];     // Ruta del hilo

function setup() {
  createCanvas(640, 240);
  background(255);
  x = width / 2;
  y = height / 2;
}

function draw() {
  // Guardar la posición actual
  path.push({ x, y });

  // Dibujar el camino del hilo
  noFill();
  stroke(150, 0, 200, 100); // Color tipo lana morada, un poco translúcida
  strokeWeight(2);
  beginShape();
  for (let i = 0; i < path.length; i++) {
    vertex(path[i].x, path[i].y);
  }
  endShape();

  // Calcular dirección suavemente usando Perlin noise
  let angle = noise(t) * TWO_PI * 2; // Ángulo aleatorio suave
  let stepSize = 2; // tamaño del paso, como si fuera el largo del hilo

  // Actualizar posición
  x += cos(angle) * stepSize;
  y += sin(angle) * stepSize;

  // Limitar al canvas
  x = constrain(x, 0, width);
  y = constrain(y, 0, height);

  // Incrementar tiempo para variar el ruido
  t += 0.01;
}

````
- Enlace: https://editor.p5js.org/vlr1004/sketches/H5fzBfjMq
- Captura:
<img width="1919" height="808" alt="image" src="https://github.com/user-attachments/assets/c5f4a6bf-2103-49a6-ac09-413c7d30d254" />

