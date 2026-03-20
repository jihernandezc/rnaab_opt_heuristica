<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

<style> table { margin: 0 auto !important; } </style>

<div style="position: sticky; top: 0; background-color: white; padding: 10px 0; border-bottom: 1px solid #ddd; z-index: 999; text-align: center; width: 100%;">
  <a href="index.html" style="text-decoration: none;">🏠 <b>Inicio</b></a> | 
  <a href="parte1.html" style="text-decoration: none;">📈 <b>Parte 1</b></a> | 
  <a href="parte2.html" style="text-decoration: none;">🗺️ <b>Parte 2</b></a> | 
  <a href="ia.html" style="text-decoration: none;">🤖 <b>Uso de IA</b></a>
</div>

<br>

# Parte 2: Optimización Combinatoria

## 1. Introducción

Imagina que eres un gerente de logística encargado de diseñar la ruta de ventas más eficiente para cubrir todo México. El objetivo parece simple: un vendedor debe visitar exactamente una vez cada una de las **32 capitales estatales**, minimizando el costo total del trayecto y regresando al punto de partida. 

Sin embargo, detrás de esta simplicidad se esconde el **Problema del Vendedor Viajero (TSP)**, un enigma de clase **NP-hard**. Con 32 ciudades, existen aproximadamente $$2.6 \times 10^{35}$$ rutas posibles. Si intentáramos resolverlo por fuerza bruta probando una ruta por segundo, tardaríamos más que la edad actual del universo. Por ello, en esta etapa del proyecto, modelamos el costo operativo real de un vendedor en las carreteras mexicanas y desplegamos dos potentes metaheurísticas para hallar la solución óptima: **Colonias de Hormigas (ACO)** y **Algoritmos Genéticos (GA)**.


## 2. Modelado de Costos

Para que el experimento sea un reflejo fiel de la realidad logística, hemos definido los siguientes parámetros basados en el contexto actual de México:

*   **Vehículo usado por el vendedor y su rendimiento:** Para la elección del vehículo utilizado por el vendedor, hemos decidido seleccionar el **Nissan Versa**, por ser uno de los vehículos preferidos por los mexicanos, ya que este genera buen rendimiento de combustible y consideramos que es apropiado para un vendedor que debe recorrer las capitales de los 32 estados mexicanos. El rendimiento del combustible del vehículo es de 18.5 km/L en ciudad, 24.95 km/L en carretera y **20.94 km/L combinado**. Para el caso de aplicación, nos ajustaremos al rendimiento combinado considerando que existan tramos de carretera y ciudad durante el recorrido, por lo que es el valor más cercano a la realidad.

*   **Precio del combustible:** Según un informe generado por Infobae el 9 marzo de 2026, el precio promedio nacional de la **gasolina Magna (regular)** en México se sitúa alrededor de los **$23.35 pesos por litro**. Como el Nissan Versa consume gasolina Magna, nos ajustaremos a este precio del combustible.

*   **Costo por peajes:** Para el tema de los peajes, al ser tan complejo de manejar por depender del sector y de las rutas definidas, se ha decidido implementar un costo promedio en relación con el tipo de vehículo. Según **CAPUFE**, la tarifa promedio por kilómetro para un vehículo ligero en autopistas de cuota en México es de **$2.50 MXN/km**.

*   **Salario del vendedor:** Será un parámetro a estudiar durante el desarrollo del caso de aplicación, pero inicialmente estableceremos un valor base de **43.0 MXN/hora**, lo cual suele ser un salario promedio para un vendedor del país según Indeed.

*   **Velocidad media:** Respetando los límites de velocidad máxima establecidos según los tipos de carretera del país, se ha tomado un promedio razonable de **80 km/h**, que permite modelar la velocidad promedio del vendedor al recorrer los diferentes distritos.

## 3. Geometría Planetaria: La Fórmula de Haversine

Para calcular la distancia entre dos ciudades dentro de la lista de capitales, hemos decidido utilizar la **fórmula de Haversine**. Esta es crucial para identificar la distancia mínima real entre dos puntos de una esfera (nuestro planeta), ya que tiene en cuenta la **curvatura de la Tierra** a partir de las coordenadas geográficas (latitud y longitud). Al tratar con un país de la extensión de México, la curvatura no es despreciable.

$$ \text{Distancia} = 2 \cdot R \cdot \operatorname{asin} \left( \sqrt{\sin^2 \left( \frac{\Delta \text{lat}}{2} \right) + \cos(\text{lat}_1) \cdot \cos(\text{lat}_2) \cdot \sin^2 \left( \frac{\Delta \text{lon}}{2} \right)} \right)$$

**Variables y parámetros:**
* $$lat, lon$$: Latitud y Longitud.
* $$(lat_1, lon_1)$$: Coordenadas del punto de origen.
* $$(lat_2, lon_2)$$: Coordenadas del punto de destino.
* $$\Delta lat = lat_2 - lat_1$$: Diferencia de latitud entre los dos puntos.
* $$\Delta lon = lon_2 - lon_1$$: Diferencia de longitud entre los dos puntos.
* $$R = 6372.795 \text{ km}$$ (Radio medio de la Tierra).

## 4. Construcción de la Matriz de Costos

A partir de las distancias calculadas con Haversine, construimos una **Matriz de Costos**. Esta matriz es el "cerebro" que consultan nuestros algoritmos; en lugar de calcular distancias en cada paso, los algoritmos consultan esta tabla precalculada que representa el costo real en pesos mexicanos (MXN) de desplazarse entre cualquier par de ciudades.

**¿Por qué una matriz de costos?** Porque el problema no busca solo la distancia más corta, sino el trayecto más económico. El costo total que integra cada celda de nuestra matriz se compone de:

1.  **Combustible:** Depende de la distancia, el rendimiento del Nissan Versa y el precio de la gasolina Magna.
2.  **Peajes:** Estimación de $2.50 MXN/km basado en tarifas de CAPUFE.
3.  **Tiempo del vendedor:** El costo de oportunidad del salario ($43.0/h) multiplicado por el tiempo de viaje a 80 km/h.

## 5. Comparación de Algoritmos: ACO vs. GA

### Optimización por Colonia de Hormigas (ACO)
El algoritmo de **Optimización por Colonia de Hormigas** (ACO: *Ant Colony Optimization*) está inspirado en el comportamiento colectivo de las hormigas reales cuando buscan las rutas más cortas hacia el alimento en el ambiente. Cuando una hormiga recorre un camino, deposita una sustancia química llamada **feromona**.
Las demás hormigas tienden a seguir los caminos con mayor cantidad o concentración de feromonas, lo que con el paso del tiempo (iteraciones) va haciendo más fuertes las rutas más cortas.

El algoritmo utiliza dos parámetros clave para equilibrar la explotación y exploración:
- **α (alpha):** controla la importancia de la feromona acumulada.
- **β (beta):** controla la importancia de la información heurística, es decir, el inverso del costo entre ciudades.

En cada iteración, las feromonas se **evaporan** parcialmente, esto está controlado por un parámetro (parámetro **ρ**), lo que evita que el algoritmo quede atrapado en soluciones que sean óptimos locales y favorece la exploración de nuevas rutas.

Por otro lado, el parámetro **Q** determina la cantidad de feromonas que cada hormiga deposita en un determinado camino o trayecto al completar su recorrido, esto significa que, una hormiga que encontró una ruta de menor costo deposita más feromona (Q/costo), reforzando más fuertemente ese camino para las iteraciones siguientes.

<div style="text-align: center;">
    <img src="https://media.githubusercontent.com/media/jihernandezc/rnaab_opt_heuristica/main/output/opt_combinatoria/tsp_aco.gif" width="800" />
    <p><em>Figura 1: Proceso de optimización de ACO. Se observa una mejora gradual y continua gracias al refuerzo colectivo.</em></p>
</div>

### Algoritmo Genético (GA)
El **Algoritmo Genético** (GA: *Genetic Algorithm*) está inspirado en el proceso de evolución natural de las especies. El
algoritmo se fundamenta en una **población** de soluciones candidatas (*rutas*) que evoluciona generación tras generación, reduciendo los costos de cada individuo, mediante tres operadores principales:

- **Selección:** se escogen aleatoriamente K individuos de la
  población y el de menor costo pasa a ser padre (el mejor individuo). Esto favorece a las mejores soluciones sin descartar completamente las peores, manteniendo diversidad y un equilibrio entre exploración y explotación.

- **Cruce:** dados dos padres, se copia un segmento
  aleatorio del primero al hijo y se completa con las ciudades restantes en el orden en que aparecen en el segundo padre. Esto garantiza que el hijo sea siempre una ruta válida (sin ciudades repetidas ni faltantes, y que se haya generado una nueva ruta a partir de la información que le han heredado sus padres).

- **Mutación:** con una probabilidad controlada por el
  parámetro **mut_rate**, se intercambian dos ciudades aleatorias de la ruta, este cambio introduce pequeñas variaciones para evitar que la población converja muy rápido a un óptimo local, y se puedan seguir explorando soluciones.

Adicionalmente se aplica **elitismo**, lo cual hace que las mejores soluciones de cada generación pasan directamente a la siguiente sin ser modificadas, garantizando que la mejor solución encontrada nunca se pierda.

<div style="text-align: center;">
    <img src="https://media.githubusercontent.com/media/jihernandezc/rnaab_opt_heuristica/main/output/opt_combinatoria/tsp_ga.gif" width="800" />
    <p><em>Figura 2: Evolución del Algoritmo Genético. Se notan cambios abruptos cuando un "hijo" hereda una secuencia superior.</em></p>
</div>

## 6. Análisis de Resultados: La Batalla por las Carreteras de México

Tras configurar nuestro ecosistema logístico y calibrar los motores de búsqueda, sometimos a ambos algoritmos al desafío de encontrar la ruta más económica. Los resultados no solo revelan una diferencia en los números, sino en la **estrategia de navegación** que cada inteligencia adoptó para recorrer el país.

### 6.1. Desempeño General: Calidad vs. Velocidad

Al comparar el rendimiento directo, observamos un duelo clásico en la computación evolutiva: la eficiencia bruta del Algoritmo Genético frente a la precisión meticulosa de la Colonia de Hormigas.

<div align="center" markdown="1">

### Tabla 1: Comparativa de Resultados

| Métrica | Colonia de Hormigas (ACO) | Algoritmo Genético (GA) |
| :---: | :---: | :---: |
| **Costo Total** | **$36,898.68 MXN** | $42,484.44 MXN |
| **Distancia Total** | **8,885.7 km** | 10,230.8 km |
| **Tiempo de Ejecución** | 14.94 s | **2.60 s** |
| **Veredicto** | **Más Económico** | **Más Rápido** |

> **Nota:** El ACO entrega una ruta más barata, pero a costa de un tiempo de cómputo mayor.

</div>

El **ACO** logró reducir el costo en casi **$6,000 MXN** respecto al GA. Sin embargo, esta precisión tiene un "costo" en tiempo de cómputo, siendo casi 6 veces más lento que el Algoritmo Genético. Para una empresa logística, esos 12 segundos extra de espera se traducen en un ahorro masivo de combustible y peajes en la operación real.

### 6.2. Visualización de Rutas y Convergencia

La diferencia entre ambos métodos se vuelve evidente al observar el rastro que dejaron sobre el mapa.

<div style="text-align: center;">
    <img src="https://raw.githubusercontent.com/jihernandezc/rnaab_opt_heuristica/refs/heads/main/output/opt_combinatoria/comparativa_rutas.png" width="700" />
    <p><em>Figura 3: A la izquierda, el ACO muestra una ruta fluida que barre el país de sureste a noroeste. A la derecha, el GA presenta cruces ineficientes, señal clara de un estancamiento.</em></p>
</div>

*   **Ruta ACO (La lógica del experto):** Comienza con una barrido elegante desde Mérida hacia el centro, sube por el Pacífico hasta Mexicali y regresa por el norte hacia el Golfo. Es una ruta que evita zigzagueos innecesarios.
*   **Ruta GA (El caos del principiante):** Aunque encuentra conexiones buenas, comete errores costosos, como saltar de Monterrey a Tepic para luego volver a subir hacia el centro. Estos "cruces" de caminos son los que disparan el kilometraje.

<div style="text-align: center;">
    <img src="https://raw.githubusercontent.com/jihernandezc/rnaab_opt_heuristica/refs/heads/main/output/opt_combinatoria/curvas_convergencia.png" width="750" />
    <p><em>Figura 4: Evolución del costo. El GA (azul) cae rápido pero se estanca en una meseta. El ACO (rojo) inicia mejor y sigue puliendo la solución hasta el final.</em></p>
</div>

La **curva de convergencia** del GA es reveladora: cae drásticamente en las primeras generaciones pero se "aplana" rápidamente. Esto indica que el algoritmo quedó atrapado en un **óptimo local**; su población se volvió tan similar entre sí que las mutaciones ya no bastaron para encontrar mejores caminos. El ACO, por su parte, mantuvo una mejora constante gracias al sistema de feromonas que guía a las hormigas hacia zonas de mayor éxito sin perder la exploración.

### 6.3. Análisis de Sensibilidad: El Impacto del Salario

¿Qué sucede si el sueldo del vendedor sube? ¿Cambiaría la ruta para priorizar la velocidad sobre la distancia? Realizamos cinco simulaciones variando el salario por hora para observar la flexibilidad de los algoritmos.

<div style="text-align: center;">
    <img src="https://raw.githubusercontent.com/jihernandezc/rnaab_opt_heuristica/refs/heads/main/output/opt_combinatoria/curvas_convergencia.png" width="700" />
    <p><em>Figura 5: Impacto del salario en el costo total. La brecha de $6,000 MXN entre ACO y GA se mantiene constante en todos los niveles.</em></p>
</div>

#### Hallazgos Clave del Análisis:

1.  **Superioridad Consistente:** Independientemente de si el salario es de $20/h o $150/h, el **ACO siempre encontró una ruta mejor**. La diferencia promedio se mantuvo alrededor de los $6,000 MXN, confirmando que el ACO es el método más robusto para la escala geográfica de México.
2.  **La Tiranía de los Peajes:** Al observar el desglose por componentes, notamos algo fascinante: **los peajes son el gasto dominante**. Representan más del 60% del costo total. Esto explica por qué la ruta óptima casi no cambia al subir el salario; el costo de las casetas es tan alto que la prioridad siempre será recorrer menos kilómetros, incluso si eso implica que el vendedor pase más tiempo al volante.
3.  **Invariabilidad de la Ruta:** La distancia de la mejor ruta del ACO osciló apenas entre 8,820 y 8,886 km. Esto demuestra que la estructura de costos en México está "anclada" a la infraestructura vial; no importa cuánto gane el vendedor, la ruta más barata siempre será la que minimice el paso por casetas y el consumo de gasolina.


<div style="text-align: center;">
    <img src="https://raw.githubusercontent.com/jihernandezc/rnaab_opt_heuristica/refs/heads/main/output/opt_combinatoria/barras_apiladas.png" width="700" />
    <p><em>Figura 6: Desglose de costos. El combustible y los peajes (dependientes de la distancia) asfixian el impacto del salario en la toma de decisiones.</em></p>
</div>

### 6.4. Conclusión del Análisis

El experimento es contundente: para problemas de logística nacional en México, **la inteligencia colectiva (ACO) triunfa sobre la evolución genética tradicional (GA)**. 

Mientras que el Algoritmo Genético es una opción atractiva si se requiere una respuesta en milisegundos, su tendencia a estancarse en rutas ineficientes lo vuelve costoso para la operación real. El ACO, aunque más demandante para el procesador, "entiende" mejor la continuidad del mapa, entregando una ruta lógica que respeta tanto el presupuesto como la geografía nacional. En este duelo, las hormigas no solo llegaron más rápido a la meta, sino que lo hicieron gastando mucho menos.

## 7. Referencias

Caminos y Puentes Federales (CAPUFE). (2025). *Tarifas vigentes 2025*. Gobierno de México. https://pot.capufe.mx/gobmx/transparencia/tarifas.html

Google Maps. (2026). *Coordenadas geográficas de las 32 capitales estatales de México*. https://www.google.com/maps

Indeed México. (2025). *Sueldo de vendedor/a en México*. Indeed. https://mx.indeed.com/career/vendedor/salaries

Infobae. (2026, 9 de marzo). *Gasolina en México: precio de la magna, premium y diésel este 9 de marzo*. https://www.infobae.com/mexico/2026/03/09/gasolina-en-mexico-precio-de-la-magna-premium-y-diesel-este-9-de-marzo/

Nissan News México. (2024, 14 de marzo). *¿Por qué Nissan Versa es el auto favorito de los mexicanos?* Nissan México. https://mexico.nissannews.com/es-MX/releases/por-que-nissan-versa-es-el-auto-favorito-de-los-mexicanos

Python Inicios. (2016, 11 de marzo). *¿Cómo calcular la distancia entre dos puntos geográficos?* Telecomunicaciones y Desarrollo de Software. http://pythoninicios.blogspot.com/2016/03/como-calcular-la-distancia-entre-dos.html

Sanborns Seguros. (2023, 30 de noviembre). *Límites máximos de velocidad en las carreteras de México 2024*. Blog Sanborns. https://www.sanborns.com/es-us/blog/limites-maximos-de-velocidad-en-las-carreteras-de-mexico-2024/