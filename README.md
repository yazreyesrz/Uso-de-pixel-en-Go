# Uso-de-pixel-en-Go
# Uso de Pixel en Go para Animaciones, Movimientos y cómo agregarlo en una simulación

En la implementación de un simulador de estacionamiento, uno de los aspectos más interesantes fue el uso de la librería Pixel para animar los vehículos y representar visualmente sus movimientos de manera fluida. A continuación, te explicaré cómo utilicé esta librería para lograr animaciones dinámicas, centrándome en el proceso general de animación en Pixel, con ejemplos básicos que ilustran cómo funciona esta librería.

# ¿Por qué Pixel?
Pixel es una librería de gráficos 2D escrita en Go, diseñada para facilitar la creación de aplicaciones visuales interactivas, como juegos o simuladores. Entre sus características más destacadas se encuentran:

- Rendimiento optimizado: Gracias a su implementación sobre OpenGL, Pixel asegura un manejo eficiente de gráficos incluso con cargas elevadas.
- Versatilidad: Pixel no solo permite manejar gráficos, sino también entradas de teclado y ratón, lo que facilita la interacción con el usuario.
- Soporte para animaciones: Con Pixel, es posible manejar transformaciones, rotaciones y escalas de manera fluida.

Elegí Pixel porque simplificó el manejo gráfico, permitiéndome concentrarme en la lógica del simulador sin complicarme con los detalles del renderizado.

# Principios Básicos de Animación con Pixel
La animación en Pixel se basa en tres elementos principales:

- Sprites: Representan imágenes que se dibujan en pantalla.
- Matrices de transformación: Se utilizan para cambiar la posición, tamaño o rotación de los sprites.
- Ciclo de actualización: Un bucle principal que actualiza las posiciones y redibuja los elementos en pantalla a intervalos regulares.

# Mi Implementación de Pixel en un Simulador
En mi proyecto, el objetivo era simular vehículos que se mueven a través de un estacionamiento y que interactúan entre sí. A continuación, te explico los aspectos clave de la implementación:

- Carga de Recursos:
El primer paso para animar en Pixel es cargar las imágenes que representarán los objetos animados, como vehículos o personajes. Estas imágenes se convierten en sprites.

```bash
func CargarSprite(ruta string) *pixel.Sprite {
    img, err := loadPicture(ruta)
    if err != nil {
        panic(err)
    }
    return pixel.NewSprite(img, img.Bounds())
}
```
La función loadPicture carga una imagen desde un archivo. El sprite resultante se puede dibujar en cualquier posición de la ventana.

- Movimiento y Transformaciones:
Pixel utiliza matrices de transformación para mover, rotar o escalar sprites. Por ejemplo, para mover un sprite, se utiliza la transformación pixel.IM.Moved.

Por ejemplo:

```bash
func DibujarSprite(win *pixelgl.Window, sprite *pixel.Sprite, posicion pixel.Vec) {
    matriz := pixel.IM.Moved(posicion) 
    sprite.Draw(win, matriz)         
}
```
La posición del sprite se representa mediante un vector (pixel.Vec), que contiene las coordenadas en 2D.

- Interpolación para Movimiento Suave:
La interpolación es una técnica que permite calcular posiciones intermedias entre un punto inicial y un punto final, logrando movimientos más fluidos.

```bash
func InterpolarPosicion(actual, destino pixel.Vec, factor float64) pixel.Vec {
    return actual.Add(destino.Sub(actual).Scaled(factor))
}
```
En este ejemplo, actual es la posición actual del objeto, destino es la posición objetivo y factor controla la velocidad del movimiento (por ejemplo, 0.1 significa un movimiento lento y suave).

- Ciclo Principal de Animación:

El ciclo principal es donde ocurre la magia. Este bucle se ejecuta continuamente, actualizando las posiciones de los objetos y redibujándolos en la ventana.

```bash
func CicloPrincipal(win *pixelgl.Window, sprite *pixel.Sprite, posicion pixel.Vec, destino pixel.Vec) {
    for !win.Closed() {
        // Limpia la pantalla
        win.Clear(colornames.Skyblue)

        // Calcula la nueva posición interpolada
        posicion = InterpolarPosicion(posicion, destino, 0.1)

        // Dibuja el sprite en la nueva posición
        DibujarSprite(win, sprite, posicion)

        // Actualiza la ventana
        win.Update()
    }
}
```
En este ciclo:
- Se limpia la pantalla para preparar el siguiente fotograma.
- Se calcula la nueva posición del objeto utilizando interpolación.
- Se dibuja el objeto en la nueva posición.
- Se actualiza la ventana para reflejar los cambios.

La capacidad de Pixel para manejar transformaciones hizo que fuera sencillo implementar el movimiento de mi simulador. Además, el uso de texturas precargadas ayudó a optimizar el rendimiento gráfico.


# ¿Cómo Pixel Ayuda en los Proyecto?

Pixel jugó un papel clave en la visualización del simulador. Algunos aspectos importantes donde Pixel fue invaluable incluyen:

- Animaciones fluidas: Pixel facilita el movimiento de los vehículos en tiempo real, lo que mejora la experiencia visual.
- Manejo de gráficos 2D: Con Pixel, pude cargar y manipular imágenes de vehículos, carriles y fondo de manera sencilla.
- Interactividad: Implementar entradas del teclado para pausar la simulación o ajustar parámetros fue intuitivo gracias a la integración de Pixel con eventos de usuario.

# Retos y Soluciones

- Manejo de Concurrencia:
El acceso simultáneo a datos compartidos, como la lista de vehículos, podía causar inconsistencias. Implementé candados (sync.Mutex) y canales para evitar condiciones de carrera.

- Sincronización entre Lógica y Gráficos:
Mantener la lógica y el renderizado sincronizados fue desafiante. La solución fue implementar un ciclo principal basado en el tiempo, que aseguraba actualizaciones constantes de las posiciones y el redibujado:

```bash
go
func run() {
    for !win.Closed() {
        DibujarVehiculos(win, sprite)
        win.Update()
    }
}
```
- Animaciones de Rotación y Escalado:
Además de mover objetos, Pixel permite rotarlos y escalarlos con facilidad. Aquí un ejemplo de cómo rotar un sprite alrededor de su centro:

```bash
func RotarSprite(win *pixelgl.Window, sprite *pixel.Sprite, posicion pixel.Vec, angulo float64) {
    matriz := pixel.IM.Rotated(posicion, angulo) // Rotación alrededor de 'posicion'
    sprite.Draw(win, matriz)
}
```
El ángulo de rotación se expresa en radianes, y la rotación se realiza alrededor de un punto específico, como el centro del sprite.

- Manejo de Tiempo en Animaciones:
Para garantizar que las animaciones tengan una velocidad consistente independientemente de la velocidad del hardware, se puede utilizar un temporizador.

```bash
func CicloConTiempo(win *pixelgl.Window, sprite *pixel.Sprite, posicion pixel.Vec, velocidad pixel.Vec) {
    ultimo := time.Now()
    for !win.Closed() {
        // Calcula el tiempo transcurrido desde el último fotograma
        delta := time.Since(ultimo).Seconds()
        ultimo = time.Now()

        // Actualiza la posición en función del tiempo y la velocidad
        posicion = posicion.Add(velocidad.Scaled(delta))

        // Limpia la pantalla y dibuja el sprite
        win.Clear(colornames.Black)
        DibujarSprite(win, sprite, posicion)

        // Actualiza la ventana
        win.Update()
    }
}
```
En este caso, la posición se actualiza en función del tiempo transcurrido (delta) y la velocidad del objeto.

- Optimización del Rendimiento:
Renderizar múltiples vehículos y actualizarlos en tiempo real puede ser costoso. Para mejorar el rendimiento:

    - Precargué texturas para evitar operaciones repetitivas.
    - Reducí la cantidad de cálculos gráficos redundantes.
    - Usé interpolación para suavizar los movimientos.


# Conclusión
El uso de Pixel en Go para animaciones es una forma eficiente y poderosa de agregar dinamismo a cualquier proyecto gráfico. A través de transformaciones, interpolación y un ciclo de renderizado en tiempo real, es posible crear movimientos suaves y realistas. 

Esta librería no solo facilita el manejo de gráficos 2D, sino que también permite combinar lógica y visualización de manera fluida, lo que la convierte en una excelente opción para simulaciones, juegos y aplicaciones interactivas.

221262 - Reyes Ruiz Yazmin
