# Unidad 2

## 🔎 Fase: Set + Seek

# Actividad 1
- ¿Cómo funciona la suma dos vectores en p5.js?
    Se utiliza el `add()`, donde suma componete a componente para crear el vector.
- ¿Por qué esta línea position = position + velocity; no funciona?  
    No funciona porque velocity es un objeto, por lo que no va leerlo como número.
# Actividad 2
- ¿Qué tuviste que hacer para hacer la conversión propuesta?
      Se crea el vector para guardar la posición y uno para el movimiento/caminata, por lo que para que se mueva se añade el `add` para que pueda caminar.
- Muestra el código que utilizaste para resolver el ejercicio.
```
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
    
    this.position = createVector(width / 2, height / 2);
  }

  show() {
    stroke(0);
    point(this.position.x, this.position.y);
  }

  step() {
    const choice = floor(random(4));

  
    let step = createVector(0, 0);

    if (choice == 0) {
      step.x = 1;
    } else if (choice == 1) {
      step.x = -1;
    } else if (choice == 2) {
      step.y = 1;
    } else {
      step.y = -1;
    }

  
    this.position.add(step);
  }
}

```
# Actividad 3
- ¿Qué resultado esperas obtener en el programa anterior?:  
Espero que me de un vector, y me de otro resultado de ese vector pero en otra posición
- ¿Qué resultado obtuviste?:  
Me da el vector, pero da otro valor del vector pero cambiado.
- Recuerda los conceptos de paso por valor y paso por referencia en programación. Muestra ejemplos de este concepto en javascript.
### Paso por valor
```
function cambiarValor(x) {
  x = 10;
}

let a = 5;
cambiarValor(a);
console.log(a);  // Imprime 5, no cambia


```

### Paso por referencia 
```
function modificarObjeto(obj) {
  obj.valor = 10;
}

let miObjeto = { valor: 5 };
modificarObjeto(miObjeto);
console.log(miObjeto.valor);  // Imprime 10, sí cambia

```

- ¿Qué tipo de paso se está realizando en el código?  
Estoy utilizando un p5.Vector, que es un objeto. Por lo tanto, al pasar posicion a la función playingVector, estás trabajando con paso por referencia.Eso significa que cuando modificas v.x y v.y dentro de la función, estás modificando directamente el objeto original posicion.


- ¿Qué aprendiste?  
Aprendí la diferencia entre paso por valor y paso por referencia en JavaScript. Entendí que los valores primitivos se copian al pasarse a una función, mientras que los objetos se comparten mediante referencias. Esto me permite modificar objetos dentro de funciones y que esos cambios se reflejen afuera. En mi código, usé un vector (p5.Vector), que se pasó por referencia, por lo que los cambios realizados dentro de la función afectaron el valor original.
  
# Actividad 4
- ¿Para qué sirve el método mag()? Nota que hay otro método llamado magSq(). ¿Cuál es la diferencia entre ambos? ¿Cuál es más eficiente?  
El metodo mag() calcula el tamaño del vector (la magnitud). El devuelve la magnitud real utilizando raiz cuadrada y magSq() da el cuadrado de la magnitud. EN teoria, magSq() es más eficiente porque evita utilizar Mth.sqrt() que es mas dificil de procesarla.


- ¿Para qué sirve el método normalize()?  
Sirve cuando se quiere usar un vector solo para indicar dirección, entonces es util cuando se necesita encontrar o utilizar la dirección del vector.

- Te encuentras con un periodista en la calle y te pregunta ¿Para qué sirve el método dot()? ¿Qué le responderías en un frase?
Mide que tanto dos vectores apuntan en la misma dirección, y se usa para calcular angulos o proyecciones entre ellos.

  
- El método dot() tiene una versión estática y una de instancia. ¿Cuál es la diferencia entre ambas?  
La versión de instancia calcula el producto punto entre dos vectores y la estatica hace lo mismo pero sin necesitar que uno sea el objeto principal, lo que le permite trabajar directamente con dos vectores extrenos.

- Ahora el mismo periodista curioso de antes te pregunta si le puedes dar una intuición geométrica acerca del producto cruz. Entonces te pregunta ¿Cuál es la interpretación geométrica del producto cruz de dos vectores? Tu respuesta debe incluir qué pasa con la orientación y la magnitud del vector resultante.  
Genera un nuevo vector perpendicular, difinido por los dos vectores originales. Su magnitud representa el area de la figura formada por los dos vectores y su orientacion apunta hacia arriba o abajo según el orden de los vectores.

- ¿Para que te puede servir el método dist()?
Calcula la distancia entre dos vectores, por lo que con el se puede detectar sus colisiones, la aproximacion entre dos objetos (vectores) o calcular trayectorias.

- ¿Para qué sirven los métodos normalize() y limit()?
Normalize sirve cuando se quiere usar un vector solo para indicar dirección, entonces es util cuando se necesita encontrar o utilizar la dirección del vector y limit() limita el tamaño del vector, por lo que si se busca limitar la velocidad máxima de un objeto en movimiento se puede utilizar.
  
# Actividad 5

- Codigo:
```
function setup() {
    createCanvas(300, 300);
}

function draw() {
    background(200);

    let v0 = createVector(50, 50);
    let v1 = createVector(200, 0); //vector rojo
    let v2 = createVector(0, 200); //vector azul
    let tiempo = (sin(frameCount*0.02)+1)/2;
    let v3 = p5.Vector.lerp(v1, v2, tiempo);
    let ColorRamp = lerpColor('red','blue', tiempo);
    let PuntaRoja = p5.Vector.add(v0,v1);
    let PuntaAzul = p5.Vector.sub(v2,v1);
    drawArrow(v0, v1, 'red');
    drawArrow(v0, v2, 'blue');
    drawArrow(v0, v3, ColorRamp);
    drawArrow(PuntaRoja,PuntaAzul,'rgb(0,255,44)'); //vector verde
}

function drawArrow(base, vec, myColor) {
    push();
    stroke(myColor);
    strokeWeight(3);
    fill(myColor);
    translate(base.x, base.y);
    line(0, 0, vec.x, vec.y);
    rotate(vec.heading());
    let arrowSize = 7;
    translate(vec.mag() - arrowSize, 0);
    triangle(0, arrowSize / 2, 0, -arrowSize / 2, arrowSize, 0);
    pop();
}
```
- ¿Cómo funciona lerp() y lerpColor().    
  Lerp() devuelve un nuevo vector que está a una fracción amt del camino desde v1 hasta v2.
Por ejemplo, con amt = 0.5, devuelve el punto medio entre los dos vectores y lerpColor(c1, c2, amt) realiza lo mismo pero con colores: mezcla el color c1 con el color c2 según un valor entre 0 (solo c1) y 1 (solo c2). Es útil para crear transiciones suaves de color.


- ¿Cómo se dibuja una flecha usando drawArrow()?
1. **push() y pop():** aíslan los cambios de transformación para que no afecten otros dibujos.

2. **translate(base.x, base.y):** mueve el punto de inicio de la flecha a la posición base.

3. **line(0, 0, vec.x, vec.y):** dibuja el cuerpo de la flecha desde el origen (0,0) hasta el punto final del vector.

4. **rotate(vec.heading()):** rota el sistema de coordenadas hacia la dirección del vector.

5. **translate(vec.mag() - arrowSize, 0):** se mueve al extremo del vector para dibujar la punta.

6. **triangle(...):** dibuja la cabeza de la flecha como un pequeño triángulo.


# Actividad 6: Motion 101

- Cuál es el concepto del marco motion 101 y cómo se interpreta geométricamente.  
Es un movimiento utilizando vectores, hay dos puntos importantes:
1. La posición con la velocidad sumada a la velocidad del vectos
2. Dibujar el objeto en la posición nueva  
Geometricamente se representa en el espacio definido por su posición y la velocidad es otro vector que indica su dirección y que rapidez tiene al moverse; por lo que al sumar estas dos cosas, es como si el objeto siguiera una flecha de la velocidad en cada cuadro.
  
- ¿Cómo se aplica motion 101 en el ejemplo?  
El objeto que se utilizaz en motion 101 es 'Mover' porlo que se eplica las propiedades habladas: position y velocity. Para mover el objeto se utiliza update() donde se suma el vector de velocidad. Se utiliza show() para dibujar el el objero, por lo que se utiliza position para ubicarlo en *x* y *y*, ayudado con checEdges() mueven el objeto reapareciendolo en el lado opuesto de la pantalla cuando llega a un borde, como si el espacio fuera un mundo cíclico y draw() para llamar los anteriores y mostrarlos en orden.
  
# Actividad 7

## La meta de la programación del movimiento es llegar a tener un algoritmo para calcular la aceleración y luego dejar que el efecto de goteo (?) haga su magia

- ¿Qué observaste cuando usas cada una de las aceleraciones propuestas?  
Los pequeños cambios al calcular la aceleración, ya sea comparandolas entre si o diferentes datos en las mismas; sus trayectorias eran diferentes, no se movian de la misma manera. La constantes da un movimiento recto, estable y predecible, el aleatio es descontrolado y brusco, y el del mouse es suave ya que es guiado por el movimiento del mouse.
