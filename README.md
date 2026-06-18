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
1. Primero cargar el Dataset  con Pandas y revisar la cantidad de filas (en este caso comprobé que eran 10.000 lo cuál es un número suficiente para poder trabajar), también revisaré la estrutura de los datos y generaré gráficos de distribución para entender cómo se comportan las ptoencias y detectar si hay valores atípicos
2. Para darle mejores pistas a los modelos crearé una nueva variable numérica que sume los balances de los tres nodos de consumo, así tendré la "potencia total demandada" por el sistema
3. Entrenaré y compararé los dos algoritmos mencionados
4. Dividiré el dataset usando un 80% de los datos para el entrenamiento y un 20% para el testeo. Evaluaré el desempeño mediante Accuracy y analizaré los errores usando una matriz de confusión. 
