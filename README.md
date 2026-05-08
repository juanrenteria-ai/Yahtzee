# Simulación del Juego Yahtzee con el Método de Montecarlo

**Curso:** Simulación
**Bloque:** 2 | **Módulo:** 1 | **Actividad:** Didáctica 2
**Archivo principal:** `Simulacion_01.ipynb`

---

## Tabla de contenidos

1. [Descripción general](#1-descripción-general)
2. [Objetivo](#2-objetivo)
3. [Reglas del juego Yahtzee](#3-reglas-del-juego-yahtzee)
4. [Fundamento teórico: Método de Montecarlo](#4-fundamento-teórico-método-de-montecarlo)
5. [Arquitectura del código](#5-arquitectura-del-código)
6. [Estructuras de datos](#6-estructuras-de-datos)
7. [Funciones principales](#7-funciones-principales)
8. [Parámetros de la simulación](#8-parámetros-de-la-simulación)
9. [Flujo de ejecución](#9-flujo-de-ejecución)
10. [Visualizaciones y análisis](#10-visualizaciones-y-análisis)
11. [Dependencias](#11-dependencias)
12. [Cómo ejecutar el proyecto](#12-cómo-ejecutar-el-proyecto)
13. [Resultados esperados](#13-resultados-esperados)
14. [Reflexión pedagógica](#14-reflexión-pedagógica)

---

## 1. Descripción general

Este proyecto implementa una **simulación computacional del juego de dados Yahtzee** utilizando el **método de Montecarlo** como motor de toma de decisiones. El notebook está estructurado en dos celdas con propósitos diferenciados:

| Celda | Propósito |
|-------|-----------|
| **Celda 1** | Motor del juego + demostración de una partida completa con salida detallada por consola |
| **Celda 2** | Versión analítica con visualización de resultados mediante gráficos estadísticos |

La simulación enfrenta a **dos jugadores automáticos** (agentes de IA) que toman decisiones de forma independiente usando el método de Montecarlo para estimar la estrategia óptima en cada turno.

---

## 2. Objetivo

> Comprender y aplicar el método de Montecarlo en una aplicación que permita su ejecución y la explotación de los resultados aleatorios obtenidos.

Los objetivos específicos de aprendizaje son:

- Diseñar e implementar un algoritmo de simulación estocástica.
- Aplicar el método de Montecarlo para la toma de decisiones bajo incertidumbre.
- Validar empíricamente la **distribución uniforme** del generador de números pseudoaleatorios.
- Visualizar y analizar los resultados de la simulación con herramientas de ciencia de datos.

---

## 3. Reglas del juego Yahtzee

### Mecánica del turno

Cada turno consta de hasta **3 lanzamientos de 5 dados** (uno obligatorio + dos opcionales):

1. Se lanzan los 5 dados.
2. El jugador elige cuáles dados conservar (*bloquear*) y relanza los demás.
3. Puede repetir el paso 2 una vez más (máximo 3 lanzamientos por turno).
4. Al terminar, elige una categoría disponible y anota su puntuación.

### Sistema de puntuación (13 categorías)

El marcador se divide en sección superior e inferior:

#### Sección superior

| Categoría | Puntuación |
|-----------|-----------|
| `ones` | Suma de todos los dados con valor 1 |
| `twos` | Suma de todos los dados con valor 2 |
| `threes` | Suma de todos los dados con valor 3 |
| `fours` | Suma de todos los dados con valor 4 |
| `fives` | Suma de todos los dados con valor 5 |
| `sixes` | Suma de todos los dados con valor 6 |

#### Sección inferior

| Categoría | Condición | Puntuación |
|-----------|-----------|-----------|
| `threeOfKind` | Al menos 3 dados iguales | Suma total de los 5 dados |
| `fourOfKind` | Al menos 4 dados iguales | Suma total de los 5 dados |
| `fullHouse` | Un par + un trío | **25 puntos** fijos |
| `smallStraight` | 4 valores consecutivos | **30 puntos** fijos |
| `largeStraight` | 5 valores consecutivos | **40 puntos** fijos |
| `yahtzee` | Los 5 dados muestran el mismo valor | **50 puntos** fijos |
| `chance` | Sin condición (comodín) | Suma total de los 5 dados |

### Duración de la partida

- **13 rondas** × **2 jugadores** = **26 turnos** totales.
- Cada jugador debe llenar exactamente una categoría por turno.
- Gana quien acumule más puntos al completar las 13 categorías.

---

## 4. Fundamento teórico: Método de Montecarlo

### Concepto

El método de Montecarlo es una técnica de simulación numérica que estima el valor de una magnitud mediante la generación de un gran número de muestras aleatorias. Se basa en la **Ley de los Grandes Números**: el promedio de muchas muestras aleatorias independientes converge al valor esperado real.

### Aplicación en el proyecto

El método se aplica para resolver el problema de decisión: **¿qué dados debo conservar en cada lanzamiento para maximizar mi puntaje final?**

Para cada posible estrategia de bloqueo, el agente:

1. Fija los dados que se conservan.
2. Simula `SIMS_PER_MASK` veces los lanzamientos restantes con valores aleatorios.
3. Calcula la mejor puntuación posible en cada simulación.
4. Promedía los resultados → obtiene el **valor esperado estimado** `E[puntaje | estrategia]`.
5. Elige la estrategia con mayor valor esperado.

### Espacio de decisión

Con 5 dados, hay **2⁵ = 32 máscaras de bloqueo posibles** (desde conservar ningún dado hasta conservarlos todos). El motor evalúa las 32 en cada decisión.

### Distribución de probabilidad

Los dados utilizan una **distribución uniforme discreta**:

```
P(X = k) = 1/6 ≈ 16.67%   para k ∈ {1, 2, 3, 4, 5, 6}
```

---

## 5. Arquitectura del código

```
Simulacion_01.ipynb
│
├── [Celda 1] Motor del juego
│   ├── Parámetros Montecarlo (SIMS_PER_MASK, SEED)
│   ├── Utilidades de dados (roll_single_die, roll_dice, has_sequence)
│   ├── Sistema de puntuación (score_hand, best_category_score)
│   ├── Estructuras de datos (PlayerState, GameStats, GameState)
│   ├── Motor Montecarlo (all_keep_masks, simulate_expected_value_after_keeps,
│   │                      choose_keep_mask_montecarlo)
│   ├── Flujo del juego (play_one_turn, play_game)
│   └── Presentación de resultados (print_scoreboard, print_stats)
│
└── [Celda 2] Análisis didáctico
    ├── (Misma lógica de juego, verbose=False)
    └── Visualización (analisis_didactico_resultados)
        ├── Tabla de puntajes por categoría (pandas DataFrame)
        ├── Gráfico 1: Comparación de puntajes por jugador (seaborn barplot)
        └── Gráfico 2: Distribución empírica de caras vs. teórica uniforme
```

---

## 6. Estructuras de datos

### `PlayerState`

Encapsula el estado completo de un jugador durante la partida.

```python
@dataclass
class PlayerState:
    name: str                            # Nombre del jugador
    scores: Dict[str, Optional[int]]     # Mapa categoría → puntaje (None si no jugada)
    total: int                           # Acumulado de puntos
```

**Métodos:**
- `available_categories()` → devuelve las categorías aún sin usar.
- `set_score(category, points)` → registra el puntaje y recalcula el total.

---

### `GameStats`

Registra métricas estadísticas durante toda la partida para la validación Montecarlo.

```python
@dataclass
class GameStats:
    total_rolls: int       # Dados individuales lanzados en toda la partida
    face_hist: Counter     # Histograma: cara (1-6) → cantidad de apariciones
```

---

### `GameState`

Estado global del juego en cualquier momento de la partida.

```python
@dataclass
class GameState:
    players: List[PlayerState]  # Los 2 jugadores
    current_player_idx: int     # Quién juega ahora (0 o 1)
    turn_in_round: int          # Ronda actual (0 a 12)
    dice_values: List[int]      # Valores actuales de los 5 dados
    locked: List[bool]          # Estado de bloqueo por dado
    rolls_left: int             # Lanzamientos restantes en el turno (máx 3)
    stats: GameStats            # Objeto de estadísticas acumuladas
```

**Métodos:**
- `reset_turn()` → reinicia dados y contadores para un nuevo turno.
- `current_player` (propiedad) → devuelve el jugador en turno.
- `advance_player()` → alterna el turno y avanza la ronda si corresponde.

---

## 7. Funciones principales

### Utilidades de dados

| Función | Descripción |
|---------|-------------|
| `roll_single_die()` | Lanza un dado de 6 caras con distribución uniforme. Retorna `int` en `[1, 6]`. |
| `roll_dice(values, locked)` | Relanza los dados **no bloqueados**, modificando `values` in-place. |
| `has_sequence(values, needed_len)` | Detecta si existe una secuencia de valores consecutivos de longitud mínima `needed_len`. Elimina duplicados antes de buscar. |

---

### Sistema de puntuación

| Función | Descripción |
|---------|-------------|
| `score_hand(category, dice)` | Calcula el puntaje de una mano de 5 dados en la categoría indicada. Retorna `0` si los dados no cumplen la condición. |
| `best_category_score(dice, available)` | **Estrategia greedy**: evalúa todas las categorías disponibles y retorna la que maximiza el puntaje inmediato. |

---

### Motor Montecarlo

| Función | Descripción |
|---------|-------------|
| `all_keep_masks()` | Genera las **32 máscaras** posibles de bloqueo (combinaciones binarias de 5 bits). |
| `simulate_expected_value_after_keeps(...)` | **Núcleo del método**: estima `E[puntaje]` dado un estado de dados y una estrategia de bloqueo mediante `sims` simulaciones. |
| `choose_keep_mask_montecarlo(...)` | **Función de decisión principal**: evalúa las 32 máscaras, calcula el valor esperado de cada una y elige la óptima. |

#### Detalle: `simulate_expected_value_after_keeps`

```
Entradas:
  current_values      → valores actuales de los 5 dados
  keep_mask           → qué dados conservar (máscara booleana)
  rolls_remaining     → lanzamientos restantes después de esta decisión
  available_categories → categorías disponibles del jugador
  sims                → número de simulaciones Montecarlo

Proceso:
  Para cada simulación:
    1. Conservar los dados según la máscara
    2. Relanzar aleatoriamente los dados libres (rolls_remaining veces)
    3. Calcular la mejor puntuación posible con la mano resultante

Salida:
  Promedio de puntajes → E[puntaje | estrategia]  (aproximación Montecarlo)
```

---

### Flujo del juego

| Función | Descripción |
|---------|-------------|
| `play_one_turn(state, verbose)` | Ejecuta un turno completo: tirada inicial → hasta 2 rerolls guiados por MC → registro de categoría. |
| `play_game(verbose_each_turn)` | Orquesta la partida completa: 13 rondas × 2 jugadores. |

---

## 8. Parámetros de la simulación

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| `SIMS_PER_MASK` | `300` | Simulaciones por máscara de bloqueo. Mayor valor = mayor precisión, mayor tiempo de cómputo. |
| `SEED` | `42` | Semilla del generador pseudoaleatorio. Garantiza **reproducibilidad** (mismos resultados en cada ejecución). Cambiar a `None` para resultados no deterministas. |

### Impacto de `SIMS_PER_MASK`

```
300 simulaciones × 32 máscaras × ~2 decisiones/turno × 26 turnos ≈ 499,200 simulaciones totales
```

Este volumen garantiza que el promedio converja al valor esperado real (Ley de Grandes Números).

---

## 9. Flujo de ejecución

```
play_game()
│
├── Ronda 1 a 13
│   ├── Turno Jugador 1
│   │   ├── reset_turn()              → dados = [0,0,0,0,0], locked = [F,F,F,F,F], rolls_left = 3
│   │   ├── roll_dice() [Tirada 1]    → lanza los 5 dados (obligatorio)
│   │   ├── choose_keep_mask_MC()     → evalúa 32 máscaras × 300 sims → elige la óptima
│   │   ├── roll_dice() [Tirada 2]    → relanza los dados no bloqueados
│   │   ├── choose_keep_mask_MC()     → nueva decisión de bloqueo
│   │   ├── roll_dice() [Tirada 3]    → relanzamiento final (si el agente no se planta)
│   │   ├── best_category_score()     → elige la categoría con mayor puntaje inmediato
│   │   └── player.set_score()        → registra el puntaje y actualiza el total
│   │
│   └── Turno Jugador 2 (mismo proceso)
│
└── Resultados finales
    ├── print_scoreboard() / DataFrame → tabla de puntajes por categoría
    ├── Gráfico 1: barras comparativas por categoría
    ├── print_stats() / análisis       → distribución de caras (validación del dado)
    └── Gráfico 2: frecuencia empírica vs. probabilidad teórica (16.67%)
```

---

## 10. Visualizaciones y análisis

### Gráfico 1 – Comparación de puntajes por categoría

- **Tipo:** Diagrama de barras agrupado (`seaborn.barplot`)
- **Eje X:** Las 13 categorías del Yahtzee
- **Eje Y:** Puntos obtenidos por cada jugador
- **Propósito:** Comparar el rendimiento de los dos agentes en cada categoría e identificar dónde el método Montecarlo fue más efectivo.

### Gráfico 2 – Distribución empírica de caras

- **Tipo:** Diagrama de barras + línea de referencia (`seaborn.barplot` + `axhline`)
- **Eje X:** Caras del dado (1 a 6)
- **Eje Y:** Frecuencia relativa (%)
- **Línea roja punteada:** Probabilidad teórica uniforme (16.67%)
- **Propósito:** Validar que el generador pseudoaleatorio produce una distribución uniforme, fundamento estadístico del método Montecarlo.

> A mayor número de lanzamientos, las barras azules convergen a la línea roja: esto es la **Ley de los Grandes Números** en acción.

---

## 11. Dependencias

| Librería | Uso |
|----------|-----|
| `random` | Generación de números pseudoaleatorios (base del método Montecarlo) |
| `collections.Counter` | Conteo de frecuencias de caras y combinaciones de dados |
| `collections.defaultdict` | Diccionarios con valor por defecto |
| `dataclasses` | Definición compacta de clases de datos (`PlayerState`, `GameState`, `GameStats`) |
| `typing` | Anotaciones de tipo (`List`, `Dict`, `Optional`, `Tuple`) |
| `matplotlib.pyplot` | Generación y renderización de gráficos |
| `seaborn` | Visualizaciones estadísticas de alto nivel |
| `pandas` | Estructuras de datos tabulares (`DataFrame`) para el análisis comparativo |

Las primeras cinco son parte de la biblioteca estándar de Python. Las tres últimas requieren instalación:

```bash
pip install matplotlib seaborn pandas
```

---

## 12. Cómo ejecutar el proyecto

### Requisitos

- Python 3.8 o superior
- Jupyter Notebook o JupyterLab

### Pasos

```bash
# 1. Instalar dependencias (si no están instaladas)
pip install matplotlib seaborn pandas jupyter

# 2. Abrir el notebook
jupyter notebook Simulacion_01.ipynb
```

### Ejecución recomendada

1. **Celda 1** (`verbose_each_turn=True`): ejecuta la partida mostrando cada tirada y decisión en consola. Ideal para comprender el flujo paso a paso.
2. **Celda 2** (`verbose_each_turn=False`): ejecuta la partida en silencio y genera los gráficos de análisis. Ideal para la presentación de resultados.

> Ambas celdas son independientes y autosuficientes; pueden ejecutarse por separado.

### Modificar parámetros

Editar al inicio de cualquier celda:

```python
SIMS_PER_MASK = 300   # Aumentar para mayor precisión (ej: 500, 1000)
SEED = 42             # Cambiar a None para resultados no reproducibles
```

---

## 13. Resultados esperados

### Marcador final (ejemplo con SEED=42)

Al ejecutar con la semilla fija, los resultados son deterministas. La salida incluye:

```
===== MARCADOR FINAL =====
Jugador 1: XXX puntos
  - ones         : X
  - twos         : X
  ...
  - chance       : X

Jugador 2: XXX puntos
  ...

Ganador: Jugador X
```

### Estadísticas de validación

```
===== ESTADÍSTICAS =====
Total de lanzamientos (dados individuales): ~390

 Cara 1: XX veces (≈16.67%)
 Cara 2: XX veces (≈16.67%)
 ...
 Cara 6: XX veces (≈16.67%)
```

La desviación respecto al 16.67% teórico debe ser mínima, validando la calidad del generador.

---

## 14. Reflexión pedagógica

### ¿Por qué Montecarlo para Yahtzee?

El Yahtzee es un problema de decisión secuencial bajo incertidumbre: en cada tirada, el jugador desconoce los resultados futuros. Calcular la estrategia óptima exacta requeriría enumerar todos los posibles resultados (árbol de decisión exponencial). El método de Montecarlo ofrece una **aproximación eficiente**: en lugar de calcular exactitudes, muestrea el espacio de resultados y promedia.

### Conceptos demostrados

| Concepto | Cómo se manifiesta en el proyecto |
|----------|----------------------------------|
| **Distribución uniforme discreta** | Cada cara del dado tiene igual probabilidad (1/6) |
| **Ley de los Grandes Números** | La frecuencia empírica converge al 16.67% al aumentar lanzamientos |
| **Valor esperado** | `E[puntaje]` estimado promediando `N` simulaciones Montecarlo |
| **Reproducibilidad** | `SEED=42` garantiza idénticos resultados en todas las ejecuciones |
| **Estrategia greedy** | `best_category_score` maximiza el puntaje inmediato al anotar |
| **Aproximación de un paso** | El motor MC no recalcula en sub-simulaciones (simplificación intencional) |

### Limitación del diseño actual

El motor Montecarlo implementa una **aproximación de 1 paso**: al simular los lanzamientos restantes, no re-aplica el algoritmo de decisión dentro de cada simulación (usa lanzamientos directos). Una implementación más precisa usaría recursión o programación dinámica, a costa de mayor tiempo de cómputo.

---

*Proyecto académico — Simulación | Bloque 2, Módulo 1, Actividad Didáctica 2*
