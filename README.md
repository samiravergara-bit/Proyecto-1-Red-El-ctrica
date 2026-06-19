# Proyecto 1: Predicción de Estabilidad Eléctrica en una Red eléctrica de 4 nodos

Descripción del problema

Una red eléctrica debe mantener un equilibrio entre la energía generada y la consumida. Si este balance se rompe por cambios de demanda o fallas en los controladores, la red puede volver inestable y colapsar (causando apagones).

Dataset

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
1. Análisis Exploratorio (EDA): Primero cargar el Dataset con Pandas y revisar la cantidad de filas (en este caso comprobé que eran 10.000 lo cuál es un número suficiente para poder trabajar), también revisaré la estrutura de los datos y generaré gráficos de distribución para entender cómo se comportan las potencias y detectar si hay valores atípicos
2. Feature Engineering: Para darle una pista a los modelos, crearé nuevas variables numéricas
3. Feature Selection: Implementaré un Feature mutual information para seleccionar las variables que realmente aportan algo al sistema.
4. Reducción de dimensionalidad (PCA): Para optimizar el rendimiento de los modelos, aplicaré PCA para   comprimir las dimensiones sin perder información significativa. Así podré también eliminar ruido. tener un modelo más rápido y menos propenso al overfitting y me ayudará en el KNN porque al tener espacios de baja dimensión las distancias serán más significativas.
5. Estandarización: Aplicaré un escalado a los datos (StandardScaler) para que todas las variables operen con la misma escala matemática y así KNN calcule bien las distancias.
6. Entrenamiento y evaluación: Dividiré el dataset usando un 80% de los datos para el entrenamiento y un 20% para el testeo. Evaluaré el desempeño mediante Accuracy, precision, recall, F1-Score, ROC-AUC.
7. Para el control de overfitting implementaré una validación cruzada y analizaré los errores usando una matriz de confusión. 

Conclusiones 

