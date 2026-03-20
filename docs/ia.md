<div style="position: sticky; top: 0; background-color: white; padding: 10px 0; border-bottom: 1px solid #ddd; z-index: 999; text-align: center; width: 100%;">
  <a href="index.html" style="text-decoration: none;">🏠 <b>Inicio</b></a> | 
  <a href="parte1.html" style="text-decoration: none;">📈 <b>Parte 1</b></a> | 
  <a href="parte2.html" style="text-decoration: none;">🗺️ <b>Parte 2</b></a> | 
  <a href="ia.html" style="text-decoration: none;">🤖 <b>Uso de IA</b></a>
</div>

<br>

# **Reporte de Uso de Inteligencia Artificial**

Durante el desarrollo de este proyecto, nos apoyamos en herramientas de Inteligencia Artificial Generativa como Claude de Anthropic y Gemini Pro de Google, las cuales funcionaron como asistentes de programación, depuración de errores, visualización de datos y redacción del reporte.

A continuación, se describen algunos de los principales prompts que utilizamos y cómo impactaron en el resultado final:

*   **Prompt 1: Obtención de Datos Geográficos**
    > *"Dame las coordenadas (latitud, longitud) de las 32 capitales estatales de México en formato de diccionario de Python, donde la llave sea el nombre de la ciudad y el valor sea una tupla con latitud y longitud. Asegúrate de que las coordenadas correspondan al centro de cada ciudad capital, puedes obtener esta información desde Google Maps."*
    *   **¿Cómo ayudó?:** En vez de buscar manualmente las coordenadas de las 32 capitales una por una en Google Maps, le pedí directamente a la IA que me las generara en el formato exacto que necesitaba para el código. Esto me ahorró mucho tiempo y el resultado ya venía listo para pegarlo directamente en el notebook sin necesidad de cambiarle el formato.

*   **Prompt 2: Ajuste de Hiperparámetros (ACO)**
    > *"Estoy implementando un algoritmo de Colonia de Hormigas para el problema del vendedor viajero con 32 ciudades. ¿Qué valores me recomiendas usar para los parámetros alpha, beta, rho y el número de hormigas? ¿Qué pasa si pongo un valor muy alto o muy bajo en cada uno? Explícamelo de forma sencilla."*
    *   **¿Cómo ayudó?:** No tenía claro qué valores iniciales ponerle a los parámetros del ACO y no quería perder tiempo haciendo prueba y error desde cero. Con este prompt entendí rápidamente el efecto de cada parámetro y pude elegir los valores de alpha, beta y rho con una justificación clara.

*   **Prompt 3: Análisis de sensibilidad del salario**
    > *"Tengo un algoritmo de Colonia de Hormigas y un Algoritmo Genético implementados en Python... Quiero analizar cómo cambia el costo total cuando el salario varía entre 5 escenarios... ¿Cómo puedo hacer esto sin duplicar el código? Necesito una gráfica de barras apiladas al final."*
    *   **¿Cómo ayudó?:** Sabía que tenía que estudiar el salario del vendedor como parámetro pero no tenía claro cómo modificar la matriz de costos temporalmente sin dañar la que ya tenía. La IA me mostró cómo copiar y restaurar la matriz original dentro del ciclo, lo que me permitió correr los dos algoritmos para cada escenario de forma limpia y sin repetir código innecesario.

*   **Prompt 4: Animaciones con librerías específicas**
    > *"Tengo un algoritmo de optimización en Python que guarda en una lista la mejor ruta encontrada en cada iteración. Quiero hacer un GIF animado que muestre cómo va mejorando la ruta con el tiempo, donde cada frame muestre la ruta actual sobre un mapa de México y al lado una gráfica con la curva de convergencia del costo. ¿Cómo lo hago con matplotlib y PillowWriter?"*
    *   **¿Cómo ayudó?:** No tenía experiencia haciendo GIFs con matplotlib. La IA me explicó cómo usar FuncAnimation y PillowWriter para guardarlo directamente como archivo, y me mostró cómo estructurar la función para que actualizara tanto el mapa como la curva en cada frame. Me ahorró bastante tiempo de búsqueda en documentación.

*   **Prompt 5: Creación de animaciones 3D**
    > *"Tengo este código de un algoritmo de optimización. Ayúdame a crear dos códigos en Python: uno para ver el proceso de optimización en una animación 2D y otro para verlo en 3D. Haz que al final cada animación se guarde solita como un archivo GIF en la misma carpeta donde tengo mi programa"*
    *   **¿Cómo ayudó?:**  Este mensaje lo usé justo después de que ya tenía listos los algoritmos. Fue de gran ayuda porque me generó bien todas las visualizaciones y yo no sabía cómo animar gráficas en 3D con Python. Me ahorró mucho tiempo de búsqueda y pude ver exactamente cómo se movían los puntos hacia el mínimo.


*   **Prompt 6: Limpieza y Documentación Profesional**
    > *"Te paso mis códigos de los algoritmos de optimización. Porfa, ayúdame a reorganizarlos para que se vean más limpios, revisa si tengo algún error y corrígelo. También documenta cada parte del código y ponle los docstrings a todas las funciones para que se entienda bien qué hace cada una."*
    *   **¿Cómo ayudó?:** Este mensaje fue muy importante para que el proyecto final se viera mucho más profesional. La IA encontró varios fallos que yo no había visto, también dejó todo el código bien ordenado y explicado. Gracias a eso, cualquiera que lea el trabajo puede entender rápido para qué sirve cada función y qué parámetros recibe.

Además de estos prompts específicos, también utilizamos la IA para mejorar la redacción del informe final, organizar las referencias bibliográficas y darle formato académico al documento, con estructuras de prompts como:

*   *"Tomando como guía este texto y estas cifras, ayúdame a redactarlo de manera más adecuada para un blog académico."*
*   *"Ayúdame a pasar este output a una tabla en formato markdown."*
*   *"Organiza estas referencias en formato APA."*
*   *"Ayúdame a insertar una barra de navegación a un markdown."*

## **Discusión y conclusiones del equipo**

El uso de la Inteligencia Artificial en este proyexto, tuvo un impacto significativo tanto en el resultado final de la entrega, como en nuestra forma de aprender y abordar la tarea en general.

Al usar la IA como un asistente de programación, nos dimos cuenta que teniendo bases técnicas fundamentales de programación, podíamos llegar a utilizar librerías especializadas y algoritmos avanzados sin sentirnos abrumados, permitiéndonos implementar soluciones que de otro modo habrían estado fuera de nuestro alcance inicial.

Además, la IA nos ayudó a agilizar tareas mecánicas y tediosas que normalmente nos habrían consumido horas (como la recolección de coordenadas, el formateo de tablas de Markdown o la configuración de los GIFs), lo cual nos permitió enfocar nuestro tiempo en otros aspectos.

Las sugerencias de refactorización de la IA nos ayudaron a escribir un código más profesional. Aprendimos a limpiar redundancias, a modularizar nuestras funciones y a documentar con Docstrings, llevándonos como resultado un proyecto mucho más robusto, estructurado y legible.

Finalmente, la IA nos ayudó a ser más críticos y reflexivos sobre nuestro código, porque no podíamos simplemente copiar y pegar las sugerencias, sino que teníamos que entenderlas, adaptarlas a nuestro contexto y asegurarnos de que realmente mejoraban nuestro proyecto, lo cual nos llevó a entender los algoritmos de manera más eficiente.


