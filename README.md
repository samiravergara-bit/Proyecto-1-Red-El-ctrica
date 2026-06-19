# Proyecto-1-Red-Eléctrica
Utilizaré el dataset "Electrical Grid Stability Simulated Data" https://www.kaggle.com/datasets/sowlarn/ucis-electrical-grid-stability-simulated-data el cuál contiene 10.000 simulaciones numéricas de una red estrella.
El repositorio simula una red eléctrica de 4 nodos (1 nodo generador y 3 nodos de carga) y las columnas representan variables de sistemas de potencia puros:
- Tau: Tiempos de respuesta del generador y los controladores
- p: Balance de la potencia activa en los distintos nodos
- g: Coeficientes que representan cómo reacciona la carga
La idea es poder determinar si la red eléctrica de 4 nodos se mantendrá estable o colapsará (inestable) frente a las variaciones en la potencia y el tiempo de respuesta de los controladores.
Como la variable final (stabf) es una categoría estable/inestable resolveré esto como un problema de Clasificación.

Implementaré y compararé dos modelos:

1. Regresión Logística: Es un modelo estándar de clasificación binaria, es rápido y me ayudará a entender que variables (por ejemplo, si el balance de pontecia o el tiempo de respuesta) tienen mayor peso matemático en la intestabilidad de la red. La limitación es que asume que la relación entre las variables y la estabilidad es lineal y eso en sistemas de potencia reales no siempre es así.
2. K-Nearest Neighbors (KNN): Es ideal para datos numéricos continuos como los voltajes y las potencias. Al no asumir una relación lineal (que era lo que nos pasaba con regresión) puede encontrar patrones de intestabilidad agrupando escenarios similares. Si un escenario nuevo tiene parámetros muy parecidos a otros 5 escenarios que colapsaron, predecirá bien la intestabilidad. La limitación es que es sensible a outliers y requiere procesar todos los datos de entrenamiento cada vez que hace una predicción.

Metodología a aplicar:
1. Análisis Exploratorio: Primero cargar el Dataset con Pandas y revisar la cantidad de filas (en este caso comprobé que eran 10.000 lo cuál es un número suficiente para poder trabajar), también revisaré la estrutura de los datos y generaré gráficos de distribución para entender cómo se comportan las potencias y detectar si hay valores atípicos
2. Feature Engineering: Para darle mejores pistas a los modelos crearé una nueva variable numérica que sume los balances de los tres nodos de consumo, así tendré la "potencia total demandada" por el sistema
3. Control de dimensionalidad: Como el dataset solo cuenta con 12 variables y todas me serán utiles no necesito hacer aplicar PCA.
4. Estandarizaicón: Aplicaré un escalado a los datos (StandardScaler) para que todas las variables operen con la misma escala matemática y así KNN calcule bien las distancias.
5. Entrenamiento y evaluación: Dividiré el dataset usando un 80% de los datos para el entrenamiento y un 20% para el testeo. Evaluaré el desempeño mediante Accuracy y analizaré los errores usando una matriz de confusión.

Conclusiones 
Tras someter los modelos al 20% de datos ocultos para el testeo los resultados de exactitud fueron:
Exactitud Regresión Logística: 81.60%
Exactitud K-Nearest Neighbors: 85.05%

El modelo ganador fue K-Nearest Neighbors. Esto confirma que el preprocesamiento y escalado de los datos fue fundamental, ya que permitió que el algoritmo captara las reglas físicas reales detrás del colpaso de la red, logrando generalizar bien y evitando el overfitting. Cómo plantee en un inicio, KNN resulta útil en este caso porque agrupa escenarios de forma no lineal (como suele pasar en eléctrica) versus la Regresión Logística que intenta forzar esta separación mediante lineas rectas. 
