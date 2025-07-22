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
- Código:
- Enlace:
- Captura:
  
### Actividad 07
- Explica el concepto qué resultados esberabas obtener.
- Código:
- Enlace:
- Captura:
