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

# Actividad 4
1. Explica con tus palabras el objetivo y la lógica general de cálculo de cada una de las tres reglas de Flocking (Separación, Alineación, Cohesión).


2. Lista los parámetros clave identificados (radio de percepción, pesos de las reglas, maxspeed, maxforce).


3. Describe la modificación que realizaste al código y explica detalladamente el efecto que tuvo en el comportamiento colectivo del enjambre (¿Se dispersan? ¿Forman grupos compactos? ¿se mueven caóticamente?). Incluye una captura de pantalla o GIF si ilustra bien el cambio. Muestra el fragmento de código modificado.



# Apply: Actividad 5
1. Documenta todo el proceso de diseño y creación en tu bitácora, incluyendo bocetos y decisiones de diseño.  
  a. Elige un tema musical que te inspire.  
  b. Diseña una pieza de arte generativo que utilice el algoritmo de flow fields y/o flocking.  
  c. Vas a visualizar el tema musical y además vas a “tocar” las visuales, es decir, tu pieza de arte debe ser interactiva y debe permitir la interpretación en tiempo real de las visuales como si fuera un instrumento más que acompaña de manera coherente el tema musical.
2. El código fuente completo de tu sketch en p5.js.
3. Un enlace a tu sketch en el editor de p5.js.
4. Capturas de pantalla mostrando tu pieza en acción.
