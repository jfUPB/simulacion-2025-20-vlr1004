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
- ¿Para qué sirve el método normalize()?
- Te encuentras con un periodista en la calle y te pregunta ¿Para qué sirve el método dot()? ¿Qué le responderías en un frase?
- El método dot() tiene una versión estática y una de instancia. ¿Cuál es la diferencia entre ambas?
- Ahora el mismo periodista curioso de antes te pregunta si le puedes dar una intuición geométrica acerca del producto cruz. Entonces te pregunta ¿Cuál es la interpretación geométrica del producto cruz de dos vectores? Tu respuesta debe incluir qué pasa con la orientación y la magnitud del vector resultante.
- ¿Para que te puede servir el método dist()?
- ¿Para qué sirven los métodos normalize() y limit()?

  
# Actividad 5
# Actividad 6
# Actividad 7
