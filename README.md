# 🕹️ Pygame - Esquiva y Sobrevive (2D)

Videojuego 2D de supervivencia programado en **Python** con **Pygame**. Dos jugadores comparten pantalla y tienen que evitar los bloques que van cayendo desde arriba. El proyecto reúne 9 retos: límites de pantalla, obstáculos aleatorios, colisiones, pausa, reinicio, cronómetro, dificultad creciente, ajuste de velocidad y récord de puntuación.

https://github.com/carlosnunezluque/pygame-carlosnl/blob/main/gameplay-pygame-carlos-nunez-luque.mp4

## ▶️ Cómo jugar

Ejecuta el archivo principal desde la terminal:

```
python game.py
```

## 🎛️ Controles

| Acción | Jugador 1 (Azul) | Jugador 2 (Verde) |
|---|---|---|
| Subir | Flecha arriba | `W` |
| Bajar | Flecha abajo | `S` |
| Ir a la izquierda | Flecha izquierda | `A` |
| Ir a la derecha | Flecha derecha | `D` |

| Acción global | Tecla |
|---|---|
| Pausar / Reanudar | `ESC` |
| Reiniciar tras Game Over | `R` |
| Aumentar velocidad del Jugador 1 | `M` |
| Reducir velocidad del Jugador 1 | `N` |

## 📖 Cómo funciona el código

### 1. Librerías importadas

- `import pygame`: Aporta todo lo necesario para abrir ventanas, dibujar, leer el teclado, reproducir sonidos, etc.
- `import random`: Sirve para generar valores al azar.

### 2. Arranque de Pygame

- `pygame.init()`: Pone en marcha los módulos internos de Pygame. Hay que llamarlo antes de usar cualquier otra función de la librería; sin esta línea no funcionaría nada.

### 3. Ventana del juego

- `SCREEN_WIDTH = 800` y `SCREEN_HEIGHT = 600`: Guardan el ancho y el alto de la ventana en píxeles. Van en mayúsculas porque son constantes.
- `pygame.display.set_mode((800, 600))`: Crea la ventana con ese tamaño. La variable `screen` es la superficie donde se pinta todo.
- `pygame.display.set_caption(...)`: Establece el título que se ve en la barra superior de la ventana.

### 4. Paleta de colores

| Variable | Color | RGB | Uso |
|---|---|---|---|
| `WHITE` | Blanco | (255, 255, 255) | Fondo de la pantalla |
| `BLACK` | Negro | (0, 0, 0) | Textos de puntuación y tiempo |
| `RED` | Rojo | (255, 0, 0) | Obstáculos y textos de Game Over/Pausa |
| `BLUE` | Azul | (0, 0, 255) | Jugador 1 y el texto de su velocidad |
| `GREEN` | Verde | (0, 255, 0) | Jugador 2 |

### 5. Fuente y reloj

- `pygame.font.SysFont(None, 36)`: Crea la fuente con la que se escribe en pantalla. `None` indica que se usa la fuente predeterminada del sistema y 36 es el tamaño.
- `pygame.time.Clock()`: Crea un reloj para regular la velocidad del juego (FPS). Sin él, el juego correría a ritmos distintos según el equipo.

### 6. Clase Player (Jugador)

- `class Player(pygame.sprite.Sprite)`: Define el jugador heredando de Sprite, lo que da acceso a utilidades de Pygame como `.draw()`, `.update()` y la detección de colisiones.
- `def __init__(self, x, y, color)`: Es el constructor; se ejecuta solo al crear un jugador y recibe su posición inicial (x, y) y su color.
- `super().__init__()`: Llama al constructor de la clase padre (Sprite) para que quede bien inicializada.
- `self.image = pygame.Surface((40, 40))`: Genera un cuadrado de 40×40 píxeles que hace de "imagen" del jugador.
- `self.image.fill(color)`: Pinta ese cuadrado con el color recibido.
- `self.rect = self.image.get_rect()`: Obtiene el rectángulo que envuelve la imagen. Pygame lo usa para colocar al jugador y para detectar colisiones.
- `self.rect.x = x` y `self.rect.y = y`: Sitúan el rectángulo en su posición de partida.
- `self.speed = 5`: Velocidad de desplazamiento del jugador.

#### Método `update` del jugador

- `keys`: Contiene el estado de todas las teclas. Si una está pulsada, su valor es `True`.
- `Movimiento`: Con la tecla izquierda se resta `speed` a X, con la derecha se suma. Arriba resta a Y y abajo suma. (En Pygame el eje Y crece hacia abajo: Y=0 es el borde superior e Y=600 el inferior.)
- `Límites (RETO 1)`: Tras moverse, se comprueba que el jugador no se haya salido de la ventana. Por ejemplo, si `left < 0`, se recoloca en el borde izquierdo. Lo mismo ocurre con los otros tres lados.

### 7. Clase Obstacle (Obstáculo)

- `size = random.randint(20, 70)` (RETO 2): Elige un tamaño al azar entre 20 y 70 píxeles, así que cada obstáculo es distinto.
- `self.image = pygame.Surface((size, size))`: Crea el cuadrado rojo con ese tamaño.
- `self.rect.x = random.randint(0, SCREEN_WIDTH - size)`: Posición horizontal aleatoria. Se resta `size` al ancho para que el obstáculo no quede cortado por el borde derecho.
- `self.rect.y = -size`: Arranca justo por encima de la pantalla (valor negativo) para que parezca que entra cayendo.
- `self.speed_y = random.randint(3, 7)`: Velocidad de caída al azar, entre 3 y 7 píxeles por fotograma.

#### Método `update` del obstáculo

- `self.rect.y += self.speed_y`: Baja el obstáculo sumando su velocidad a la coordenada Y.
- `self.kill()`: Cuando el obstáculo sale por completo por la parte inferior, se borra del juego. Así no se acumulan objetos invisibles gastando memoria.

### 8. Variables del juego

- `player1` y `player2`: Los dos jugadores. El Jugador 1 es azul y sale en (300, 500); el Jugador 2 es verde y sale en (500, 500).
- `all_sprites`: Grupo de sprites que permite dibujar y actualizar muchos objetos con una sola instrucción. Aquí se añaden los dos jugadores.
- `obstacles`: Otro grupo, dedicado solo a los obstáculos, que se usa para detectar colisiones.
- `SPAWN_OBSTACLE = pygame.USEREVENT + 1`: Evento propio. Además de los eventos habituales de Pygame (tecla pulsada, ratón movido...), aquí creamos uno que se lanza cada cierto tiempo para generar obstáculos.
- `obstacle_spawn_time = 1000`: Un obstáculo nuevo cada 1000 ms (1 segundo).
- `pygame.time.set_timer(SPAWN_OBSTACLE, 1000)`: Indica a Pygame que dispare el evento `SPAWN_OBSTACLE` cada 1000 ms.
- `score = 0`: Puntuación de la partida (cuenta los obstáculos que han aparecido).
- `high_score = 0` (RETO 9): Récord de puntuación, que se conserva aunque se reinicie la partida.
- `start_ticks`: Guarda el instante en que comenzó la partida para calcular cuánto tiempo se sobrevive.
- `game_over = False`: Señala si la partida ha terminado (un jugador ha sido golpeado).
- `paused = False` (RETO 4): Señala si el juego está en pausa.

### 9. Bucle principal

#### 9.1 — Gestión de eventos

- `while running`: El bucle se repite mientras `running` valga `True`.
- `pygame.event.get()`: Devuelve todos los eventos ocurridos desde el fotograma anterior.
- `pygame.QUIT`: Si se cierra la ventana (clic en la X), `running` pasa a `False` y se sale del bucle.

#### 9.2 — Pausa con ESC (RETO 4)

- `pygame.KEYDOWN`: Se dispara una sola vez al pulsar una tecla, no mientras se mantiene.
- Al pulsar `ESC` se invierte el valor de `paused`: si estaba pausado se reanuda, y si no, se pausa.

#### 9.3 — Reinicio con R (RETO 5)

- Solo funciona cuando el juego ha terminado (`game_over == True`) y se pulsa `R`.
- Lo deja todo como al principio: puntuación a 0, se quita el Game Over, se reinicia el cronómetro, se vacía el grupo de obstáculos, los jugadores vuelven a su posición inicial, la velocidad del Jugador 1 regresa a 5 y el intervalo de aparición vuelve a 1 segundo.

#### 9.4 — Velocidad con M/N (RETO 8)

- `M` aumenta en 1 la velocidad del Jugador 1 (máximo 12).
- `N` la reduce en 1 (mínimo 2).
- Solo se puede cambiar mientras la partida no haya terminado.

#### 9.5 — Aparición de obstáculos y dificultad (RETO 7)

- Cada vez que salta el evento `SPAWN_OBSTACLE` se crea un obstáculo nuevo y el `score` sube 1.
- Dificultad progresiva (RETO 7): cada 10 puntos (`score % 10 == 0`) el intervalo entre obstáculos se acorta 50 ms, con un mínimo de 150 ms para que el juego no se vuelva imposible.

#### 9.6 — Lógica de juego (movimiento, colisiones y cronómetro)

- `pygame.key.get_pressed()`: Devuelve el estado de todas las teclas. A diferencia de `KEYDOWN`, detecta si la tecla sigue pulsada, lo que da un movimiento continuo y fluido.
- `player1.update(...)`: Mueve al Jugador 1 con las flechas.
- `player2.update(...)`: Mueve al Jugador 2 con las teclas WASD.
- `obstacles.update()`: Hace caer todos los obstáculos.
- Colisiones (RETO 3): Se recorren los obstáculos y se comprueba con `colliderect()` si el rectángulo de algún jugador se solapa con el de un obstáculo. Si es así, la partida termina.
- Récord (RETO 9): Al acabar, si el `score` supera el récord, este se actualiza.
- Cronómetro (RETO 6): Los segundos sobrevividos se calculan restando el tick de inicio al tick actual y dividiendo entre 1000 (de milisegundos a segundos). `//` es la división entera, sin decimales.

#### 9.7 — Dibujado en pantalla

- `screen.fill(WHITE)`: Limpia la pantalla pintándola de blanco en cada fotograma, para que los dibujos anteriores no se acumulen.
- `all_sprites.draw(screen)`: Dibuja todos los sprites (jugadores y obstáculos).
- En pausa: Se dibujan los sprites congelados y aparece el texto "PAUSE".
- `font.render(...)`: Convierte un texto en una imagen dibujable. `True` activa el antialiasing (bordes suavizados) y el tercer parámetro es el color.
- `screen.blit(imagen, (x, y))`: "Pega" una imagen en la pantalla en las coordenadas indicadas. Así se muestran los textos.
- En Game Over: Aparece centrado y en rojo el mensaje "GAME OVER - Press 'R' to Restart".
- `pygame.display.flip()`: Actualiza la pantalla. Lo dibujado con `blit` o `draw` no se ve hasta llamar a `flip()`. Pygame usa doble búfer: se dibuja en uno invisible y luego se muestra de golpe.
- `clock.tick(60)`: Limita el juego a 60 FPS, de modo que vaya igual de rápido en cualquier ordenador.

#### 9.8 — Cierre del juego

```
pygame.quit()
```

- Al salir del bucle `while`, esta línea cierra Pygame de forma limpia y libera los recursos.
