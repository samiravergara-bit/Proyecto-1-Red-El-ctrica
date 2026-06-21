# Proyecto 1: Mantenimiento predictivo y detección de anomalías en un motor industrial mediante sensores

Tuve que cambiar el dataset ya que me indicó que debían ser datos reales y no simulados, como no encontré algo exactamente igual a lo anterior con datos reales busqué algo similar, el espíritu es el mismo, sin embargo, tuve que hacer cambios debido a que similar no es lo mismo que igual.

Descripción del problema

Una planta industrial debe mantener sus motores pirncipales operativos de forma continua. Si no se detectan a tiempo las fallas de desgaste o por problemas eléctricos, el sistema puede pasar de un estado normal a colapsar deteniendo la producción.
El desafío principal es que las fallas mecánicas no ocurren de un segundo sino que son procesos que suceden en el tiempo. Identificarlas es difícil porque el motor funciona de buena manera (lo que llamaré estado "normal") la mayor parte de su vida útil y eso hace que estas fallas sean raras y haya un alto desbalance de clases.

Dataset

Utilizaré un dataset de lecturas de sensores ("sensor.cv) (https://www.kaggle.com/datasets/nphantawee/pump-sensor-data/data) el cuál contiene 220.320 registros temporales. Las columnas representan variables físicas extraídas de la máquina a lo largo de varios meses: 
- sensor_00 al sensor_51: Lectoras continuas que monitorean vibración, temperatura y comportamiento eléctrico
- machine_status: El estado real del motor en ese minuto en específico (Normal, Broken, Recovey)

La ideas es poder determinar si el motor se mantendrá "Estable" o si se encuentra en "Falla" basándome únicamente en las lecturas crudas. Resolveré esto como un sistema de Clasificación usando series de tiempo.

Implementaré y compararé dos modelos:

1. Regresión Logística: Es un modelo estándar de clasificación binaria, es rápido y me ayudará a entender que sensores tienen mayor peso matemático en detección de fallas. Como la base de datos tiene un desbalance extremo (meses de normalidad vs horas de falla) implementaré el parámeotro "class_weight="balanced"" para obligar al modelo a penalizar si pasa por alto un colapso.
2. K-Nearest Neighbors (KNN): Es ideal para datos numéricos continuos de sensores. Al no asumir una relación lineal puede encontrar patrones de intestabilidad agrupando comportamientos físicos similares. Si la lectura actual de los sensores es similar a la de un escenario histórico que terminó en en falla entonces va a predecir el colapso. Como mencioné en un inicio los tiempos de falla son my inferiores vs el funcionaineto normal del equipo por que para contrarrestarlo usaré el parámetro "weights="distance"", así las fallas que estpen más cercar del dato evaluado tendrán mayor valor predictivo.

Metodología a aplicar:

1. Análisis Exploratorio (EDA): Primero cargar el Dataset con Pandas. Eliminar los sensores que tengan un 100% de datos nulos (como el sensor_15) para evitar errores. Para los microcortes en la lectura, en lugar de rellenar con ceros utilizaré un relleno progresivo ("ffill") que mantiene el último valor válido en el tiempo.
2. Feature Engineering: Para darle una pista a los modelos, crearé la variable "vibracion_promedio" agrupando los primeros sensores de la máquina.
3. Control de la cronología: Como es una serie de tiempo usar una división aleatoria causarpia que el modelo haga trampa estudiando "el futuro" para adivinar "el pasado". Para evitarlo haré im corte manual(split) de 70% para entrenamiento y 30% para testeo, asegurando que evalúe con el último tramo de tiempo real del motor.
4. Estandarización: Aplicaré un escalado (StandardScaler) para que todas las variables (vibraciones y temperaturas) operen en la misma escala y KNN pueda calculas correctamente las distancias.
5. Feature selection (Información mutua): Aquí voy a rankear y seleccionar solo los sensores que aportan información predictiva superior a 0, eliminando el ruido del sistema.
6. Reducción de dimensionalidad (PCA): Comprimiré las dimensiones útiles buscando retener el 95% de la varianza. Esto va a acelerar el tiempo de procesamiento y eliminará redundancias.
7. Estandarización: Aplicaré un escalado a los datos (StandardScaler) para que todas las variables operen con la misma escala matemática y así KNN calcule bien las distancias.
8. Entrenamiento y evaluación: Evaluaré el desempeño mediante Accuracy, precision, recall, F1-Score, ROC-AUC. Prestando mas atencion a Recall y la matriz de confusión, ya que el mantenimiento industrial es crucial para minimizar falsos negativos (que el motor explote sin mandar una alerta)
9. En la versión anterior utilizaba validación cruzada pero como aquí todo funciona de manera secuencial en el tiempo podría mezclar futuro con pasado. El control de ajuste y sobreajuste lo realicé evaluando el modelo en un bloque temporal futuro 100% aislado (el 30%final del dataset) y analizando el comportamiento de la falsas alarmas con matrices de confusión.

Resultados obtenidos e interpretación de métricas

Para evaluar el desempeño del modelo utilcé un 30% del dataset (ordenado cronológicamente), simulando como se iba a comportar el motor en el futuro. Este bloque de prueba tenía 66.096 minutos de operación del motor dentro de los cuales solamente 76 minutos correspondían a colapso o "falla"

Resultados Regresión Logística 

Recall: 91%. El modelo detectó 69 de los 76 minutos de falla real
Precisión: 31%. Indica que de todas las veces que el modelo encedió la alarma preventiva mas o menos 1 de cada 3 era una falla real inminente, las demás fueron falsas alarmas.
Acurracy: 99.76%
ROC-AUC: 0.9976 Significa que el algortimo tenia buena capacidad para detectar entre un motor sano y uno defectuoso incluso con el balance extremo de los datos.

Resultados KNN 

Recall: 55%. El modelo detectó 42 de los 76 minutos de falla real
Precisión: 15% En este caso el acierto cuando emitió las alertas fue muy bajo, lo que generó un exceso de falsas alarmas a comparación de la Regresión Logistica.
Acurracy: 99.59%
ROC-AUC: 0.8004

Las gráficas de las matrices de confusion confirman el sesgo intencional que se le dio a los modelos. Podemos ver que en la Regresión Logística (gracias a class_weight="balanced") priorizó equivocarse lanzando advertencias de "estable" con tal de minimizar  t odas costa los espacios en blanco en la detección de una falla.

Conclusión

Lo más complicado de este proyecto fue la exactitud "perfecta". Inicialmente, hice el modelo sin el ajuste temporal y sin balancear y arrojó 99.9% de exactitud, a lo cuál tras darle muchas vueltas entendí que era porque estaba mezclando filtraciones futuras con el pasado.
Cuando corregí el enfoque y dividí el tiempo al final igual era como predecir una aguja en un pajar.

En este tipo de datasets con cronología era importante mantener la temporalidad de los datos, además, no todas las variables daban información, algunas estaban totalmente vacías y sin datos. En general el modelo es confiable porque detectó casi todos los colapsos potenciales.


