# Uso-de-pixel-en-Go
# El uso de la librería Pixel en Go y cómo agregarlo en una simulación.

Usando la Librería Pixel en Go para Crear un Simulador de Estacionamiento.
Cuando decidí crear un simulador de estacionamiento en Go, uno de los mayores retos fue cómo representar visualmente el movimiento de los vehículos de manera fluida y eficiente. Para esto, utilicé la librería Pixel. Esta librería me permitió manejar gráficos en 2D de manera sencilla y con un buen rendimiento, gracias a su uso de OpenGL. A continuación, te contaré cómo la implementé en mi proyecto y cómo puede ser útil en proyectos similares.

# ¿Por qué Pixel?
Pixel es una librería de gráficos 2D escrita en Go, diseñada para facilitar la creación de aplicaciones visuales interactivas, como juegos o simuladores. Entre sus características más destacadas se encuentran:

- Rendimiento optimizado: Gracias a su implementación sobre OpenGL, Pixel asegura un manejo eficiente de gráficos incluso con cargas elevadas.
- Versatilidad: Pixel no solo permite manejar gráficos, sino también entradas de teclado y ratón, lo que facilita la interacción con el usuario.
- Soporte para animaciones: Con Pixel, es posible manejar transformaciones, rotaciones y escalas de manera fluida.

Elegí Pixel porque simplificó el manejo gráfico, permitiéndome concentrarme en la lógica del simulador sin complicarme con los detalles del renderizado.

# Mi Implementación del Simulador de Estacionamiento
En mi proyecto, el objetivo era simular vehículos que se mueven a través de un estacionamiento y que interactúan entre sí. A continuación, te explico los aspectos clave de la implementación:

- Estructura Vehiculo:
Para representar cada vehículo, creé una estructura que almacena datos como su posición actual, su estado (estacionado o en movimiento), y un identificador único. La posición se representa con pixel.Vec, que es el tipo de coordenadas utilizado por Pixel:

go
type Vehiculo struct {
    ID                          int
    Posicion                    pixel.Vec
    PosicionAnterior            pixel.Vec
    Carril                      int
    Estacionado                 bool
    HoraSalida                  time.Time
    Entrando                    bool
    Teletransportando           bool
    TiempoInicioTeletransportacion time.Time
}

- Gestión Concurrente:
Dado que en la simulación hay múltiples vehículos operando simultáneamente, utilicé canales (chan) y candados (sync.Mutex) para manejar la concurrencia de manera segura. Aquí un ejemplo de cómo se crean nuevos vehículos:

go
var (
    CanalVehiculos  chan Vehiculo
    Vehiculos       []Vehiculo
    CandadoVehiculos sync.Mutex
)

func CrearVehiculo(id int) Vehiculo {
    CandadoVehiculos.Lock()
    defer CandadoVehiculos.Unlock()
    vehiculo := Vehiculo{
        ID:           id,
        Posicion:     pixel.V(0, 300),
        Carril:       -1,
        Estacionado:  false,
    }
    Vehiculos = append(Vehiculos, vehiculo)
    return vehiculo
}

- Cada vehículo tiene una hora de salida aleatoria, asignada con la función AsignarHoraSalida. Esto simula el tiempo que permanecerá estacionado antes de salir:

go
func AsignarHoraSalida(vehiculo *Vehiculo, duracion time.Duration) {
    vehiculo.HoraSalida = time.Now().Add(duracion)
}

- Representación Visual con Pixel:
Pixel me permitió dibujar y mover los vehículos en la pantalla de forma fluida. Para renderizar un vehículo en la posición correspondiente, utilicé la siguiente función:

go
func DibujarVehiculos(win *pixelgl.Window) {
    for _, vehiculo := range Vehiculos {
        sprite.Draw(win, pixel.IM.Moved(vehiculo.Posicion))
    }
}

La capacidad de Pixel para manejar transformaciones hizo que fuera sencillo implementar el movimiento de los vehículos de entrada a los espacios de estacionamiento. Además, el uso de texturas precargadas ayudó a optimizar el rendimiento gráfico.


# ¿Cómo Pixel Ayuda en Este Proyecto?

Pixel jugó un papel clave en la visualización del simulador. Algunos aspectos importantes donde Pixel fue invaluable incluyen:

- Animaciones fluidas: Pixel facilita el movimiento de los vehículos en tiempo real, lo que mejora la experiencia visual.
- Manejo de gráficos 2D: Con Pixel, pude cargar y manipular imágenes de vehículos, carriles y fondo de manera sencilla.
- Interactividad: Implementar entradas del teclado para pausar la simulación o ajustar parámetros fue intuitivo gracias a la integración de Pixel con eventos de usuario.

# Retos y Soluciones

- Manejo de Concurrencia:
El mayor reto fue sincronizar el acceso a los datos compartidos de los vehículos. La solución fue implementar candados para proteger las operaciones críticas.

- Sincronización entre Lógica y Gráficos:
Lograr que la lógica del simulador y el renderizado de Pixel estuvieran perfectamente sincronizados requirió diseñar un ciclo principal eficiente que actualizara ambos aspectos simultáneamente.

- Optimización del Rendimiento:
Dibujar múltiples vehículos simultáneamente puede ser costoso si no se optimiza. Precargar texturas y limitar la cantidad de operaciones gráficas redundantes ayudó a mantener la fluidez.

# Conclusión
Usar la librería Pixel en mi proyecto de simulador de estacionamiento fue una decisión acertada. Pixel no solo simplificó la implementación de gráficos y animaciones, sino que también permitió crear una experiencia interactiva y visualmente atractiva. Si estás desarrollando un proyecto que involucre gráficos 2D, Pixel es una herramienta que vale la pena explorar por su balance entre simplicidad y potencia.

Este proyecto me permitió aprender cómo integrar gráficos en tiempo real con la lógica concurrente en Go. Además, dejó la puerta abierta para futuras mejoras, como agregar colisiones entre vehículos, estadísticas visuales del estacionamiento o incluso una interfaz de usuario más interactiva.


221262 - Reyes Ruiz Yazmin
