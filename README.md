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
En mi proyecto, el objetivo era simular vehículos que se mueven a través de un estacionamiento y que interactúan entre sí. Aquí te explico cómo utilicé Pixel para representar esos vehículos y su movimiento.
Primero, creé una estructura Vehiculo, que incluye la posición del vehículo en la pantalla (Posicion), el carril en el que se encuentra y el estado de estacionado. Cada vehículo tiene una posición inicial (pixel.V(0, 300)) que representé utilizando el tipo pixel.Vec, que es la forma en que Pixel maneja las coordenadas 2D.

go
type Vehiculo struct {
    ID               int
    Posicion         pixel.Vec
    PosicionAnterior pixel.Vec
    Carril           int
    Estacionado      bool
    HoraSalida       time.Time
    Entrando         bool
    Teletransportando bool
    TiempoInicioTeletransportacion time.Time
}


Para manejar múltiples vehículos de forma concurrente, utilicé un canal de Go (CanalVehiculos) y un candado (CandadoVehiculos) para evitar condiciones de carrera. Esto me permitió generar y manipular los vehículos de manera segura.


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

Cada vehículo es asignado a una "hora de salida" aleatoria, lo que representa el tiempo que estará estacionado antes de partir, utilizando la función AsignarHoraSalida.

# ¿Cómo Pixel Ayuda en Este Proyecto?

Lo que Pixel me proporcionó fue una manera sencilla de representar estos vehículos de manera visual. Utilizando su capacidad de manejar gráficos y animaciones, pude mover los vehículos en la pantalla, cambiar sus posiciones y mostrar eventos de entrada y salida de los mismos. Esto fue clave para representar el comportamiento dinámico del estacionamiento.
Además, Pixel facilita la creación de interfaces visuales interactivas. Aunque en mi caso el enfoque principal fue la simulación, pude integrar elementos como el movimiento en tiempo real y la interacción con el usuario de manera eficiente.

# Conclusión
Usar la librería Pixel en mi proyecto de simulador de estacionamiento me permitió enfocarme en la lógica y la interacción de los vehículos sin preocuparme por los detalles complejos del renderizado gráfico. La facilidad para gestionar imágenes, animaciones y entradas del usuario me permitió crear una experiencia visual fluida y atractiva para el simulador. Si estás desarrollando un proyecto que requiera gráficos en 2D y una buena interactividad, definitivamente te recomiendo darle un vistazo a Pixel.


221262 - Reyes Ruiz Yazmin
