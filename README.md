#  Juego de la Vida de Conway

> Tarea 1 — Computación Paralela  
> LEAD University · 2026  
> Profesor: Johansell Villalobos Cubillo

---

##  Descripción

Implementación completa del **Juego de la Vida de Conway** en Python, incluyendo:

- Clase orientada a objetos `GameOfLife` con lógica vectorizada usando NumPy
- Visualización animada de patrones clásicos con Matplotlib
- Benchmark empírico de rendimiento en rejillas de 32×32 hasta 1024×1024
- Análisis de complejidad con gráficas lineales y log-log
- Simulador interactivo en HTML/JavaScript para uso en el navegador

---

##  Estructura del repositorio

```
Juego_de_vida/
├── Juego_de_vida.ipynb     # Notebook principal con toda la implementación
├── juego_de_vida.html      # Simulador interactivo (abrir en navegador)
└── README.md               # Este archivo
```

---

##  Requisitos

- Python 3.9 o superior
- Jupyter Notebook o JupyterLab

Instalar dependencias:

```bash
pip install numpy matplotlib
```

---

##  Cómo ejecutar la simulación

### Opción 1 — Jupyter Notebook

```bash
# Clonar el repositorio
git clone https://github.com/DavidMoraV/Juego-de-vida.git
cd Juego-de-vida

# Abrir el notebook
jupyter notebook Juego_de_vida.ipynb
```

Ejecutar las celdas en orden de arriba hacia abajo (`Shift + Enter`).

### Opción 2 — Simulador HTML

Abrir directamente en el navegador:

```
juego_de_vida.html
```

No requiere instalación ni servidor.

---

##  Contenido del Notebook

### 1. Clase `GameOfLife`

```python
juego = GameOfLife(filas=64, cols=64)          # estado aleatorio
juego = GameOfLife(64, 64, estado_inicial=arr) # estado manual

juego.step()          # avanza una generación
juego.run(100)        # avanza 100 generaciones
juego.get_state()     # retorna copia del tablero actual
juego.contar_vivas()  # número de celdas vivas
```

**Reglas de Conway implementadas:**

| Regla | Condición | Resultado |
|-------|-----------|-----------|
| Superpoblación | Celda viva con >3 vecinos | Muere |
| Soledad | Celda viva con <2 vecinos | Muere |
| Supervivencia | Celda viva con 2 o 3 vecinos | Sobrevive |
| Reproducción | Celda muerta con exactamente 3 vecinos | Nace |

### 2. Patrones clásicos disponibles

| Patrón | Tipo | Descripción |
|--------|------|-------------|
| `glider` | Nave espacial | Se desplaza en diagonal |
| `blinker` | Oscilador (periodo 2) | Línea que rota 90° |
| `toad` | Oscilador (periodo 2) | Dos filas escalonadas |
| `beacon` | Oscilador (periodo 2) | Dos bloques en esquinas |
| `pulsar` | Oscilador (periodo 3) | Patrón grande y simétrico |
| `lwss` | Nave espacial | Lightweight spaceship |
| `block` | Estático | Cuadrado 2×2 |
| `beehive` | Estático | Panal de abeja |

Cargar un patrón:

```python
juego = crear_patron(filas=64, cols=64, nombre='glider')
```

### 3. Visualización

```python
# Secuencia de fotogramas por generación
visualizar_evolucion('pulsar', filas=32, cols=32, pasos=8)

# Animación en tiempo real
ani = animar_juego('aleatorio', filas=64, cols=64, generaciones=100)
```

### 4. Benchmark de rendimiento

```python
TAMANOS = [32, 64, 128, 256, 512, 1024]
resultados = medir_rendimiento(TAMANOS, repeticiones=5, pasos_por_prueba=10)
graficar_rendimiento(resultados)
```

Genera dos gráficas:
- **Escala lineal** — tiempo vs. número de celdas con curvas O(n), O(n log n), O(n²)
- **Escala log-log** — para identificar visualmente la clase de complejidad

### 5. Análisis de complejidad

```python
analizar_resultados(resultados)
```

Estima el exponente de complejidad ajustando regresión lineal en escala log-log:

```
log(t) = α · log(n) + β
```

### 6. Dinámica de población

```python
graficar_poblacion('aleatorio', filas=64, cols=64, generaciones=200)
```

Traza el número de celdas vivas por generación.

---

##  Resultados de rendimiento

Resultados obtenidos en la máquina de referencia:

| Rejilla | Celdas | Tiempo por iteración |
|---------|--------|---------------------|
| 32×32 | 1,024 | ~0.13 ms |
| 64×64 | 4,096 | ~0.15 ms |
| 128×128 | 16,384 | ~0.25 ms |
| 256×256 | 65,536 | ~0.65 ms |
| 512×512 | 262,144 | ~2.3 ms |
| 1024×1024 | 1,048,576 | ~9.0 ms |

**Exponente empírico estimado: α ≈ 0.63 → complejidad O(n)**

La implementación vectorizada con NumPy escala de forma aproximadamente lineal gracias al uso de `numpy.roll` que evita bucles explícitos en Python.

---
##  Simulador HTML

El archivo `juego_de_vida.html` es un simulador autónomo que no requiere Python.

**Controles:**

| Control | Acción |
|---------|--------|
| `iniciar / pausar` | Arranca o detiene la simulación |
| `paso` | Avanza exactamente una generación |
| `limpiar` | Resetea el tablero |
| `aleatorio` | Siembra con 30% de densidad |
| Clic / arrastre | Pinta celdas vivas |
| Mayús + clic | Borra celdas |
| Slider velocidad | 1 – 60 fps |
| Slider zoom | Tamaño de celda en píxeles |

---

##  Análisis y Discusión

### Complejidad computacional

La implementación usa operaciones vectorizadas de NumPy (`numpy.roll`) que procesan toda la rejilla simultáneamente. Esto resulta en una complejidad empírica cercana a **O(n)**, donde n es el número de celdas.

### Uso de memoria

Cada celda ocupa 1 byte (`uint8`). El uso total es proporcional al tamaño:

| Rejilla | Memoria (2 buffers) |
|---------|---------------------|
| 128×128 | 32 KB |
| 512×512 | 512 KB |
| 1024×1024 | 2 MB |

### Cuellos de botella

- `numpy.roll` genera copias intermedias en memoria, lo que genera presión sobre la caché L2/L3 en rejillas grandes.
- El GIL de CPython limita la paralelización directa con `threading`.
- Para rejillas mayores a 2048×2048, se recomienda usar `scipy.ndimage.convolve` o aceleración con **Numba `@jit`**, que puede reducir el tiempo entre 5× y 20×.

---

##  Referencias

- Gardner, M. (1970). *Mathematical Games: The fantastic combinations of John Conway's new solitaire game "Life"*. Scientific American, 223(4), 120–123.
- Conway, J. H. (1970). *Game of Life*. Unpublished.
- Harris, C. R. et al. (2020). *Array programming with NumPy*. Nature, 585, 357–362.
- Hunter, J. D. (2007). *Matplotlib: A 2D graphics environment*. Computing in Science & Engineering, 9(3), 90–95.

---

##  Autor

**David Mora V.**  
Ingeniería en Ciencia de Datos — LEAD University  
Computación Paralela · 2026
