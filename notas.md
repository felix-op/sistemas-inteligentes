# 1. El script debe contener una sección de preparación de los datos

## 1. Cargá el dataset Adult. Consultar en https://archive.ics.uci.edu/dataset/2/adult

## 2. Identificá y separá las variables numéricas y categóricas
### Variables numéricas
- age
- fnlwgt
- education-num
- capital-gain
- capital-loss
- hours-per-week
### Variables categóricas
- workclass
- education
- marital-status
- occupation
- relationship
- race
- sex
- native-country
- income

## 3. Realizá un análisis descriptivo.
Se exluye education porque la columna education-num es su encoding numérico.

Se excluye fnlwgt ya que no representa a un individuo en sí sino a cuanta población representa ese registro. Este valor puede introducir un sesgo si las distancias son muy grandes.

Se excluye income ya que es el objetivo del cluster.


--------------------------------------------------------------------------------
Estadísticas Descriptivas de df numérico (Rango y Escala):
                age  education-num  capital-gain  capital-loss  hours-per-week
count  32561.000000   32561.000000  32561.000000  32561.000000    32561.000000
mean      38.581647      10.080679   1077.648844     87.303830       40.437456
std       13.640433       2.572720   7385.292085    402.960219       12.347429
min       17.000000       1.000000      0.000000      0.000000        1.000000
25%       28.000000       9.000000      0.000000      0.000000       40.000000
50%       37.000000      10.000000      0.000000      0.000000       40.000000
75%       48.000000      12.000000      0.000000      0.000000       45.000000
max       90.000000      16.000000  99999.000000   4356.000000       99.000000
--------------------------------------------------------------------------------

Se utiliza la prueba de Shapiro-Wilk
- Hipótesis Nula (H_0): La muestra proviene de una población distribuida normalmente.
- Decisión: Si el p-valor es menor que a=0.05, se rechaza H_0
por lo que los datos NO son normales.

Prueba de Normalidad de Shapiro-Wilk:

--------------------------------------------------------------------------------
                Statistic        P-Value                    Decision
age              0.966769   2.703534e-63  NO es normal (Rechazar H0)
education-num    0.926543   2.912449e-80  NO es normal (Rechazar H0)
capital-gain     0.122713  1.248080e-146  NO es normal (Rechazar H0)
capital-loss     0.218308  4.279406e-143  NO es normal (Rechazar H0)
hours-per-week   0.885072   9.528654e-91  NO es normal (Rechazar H0)
--------------------------------------------------------------------------------

## 4. Decidir que tratamiento darle a valores faltantes.

Conteo de valores faltantes (NaN) por columna:

--------------------------------------------------------------------------------
age                  0
workclass         1836
fnlwgt               0
education            0
education-num        0
marital-status       0
occupation        1843
relationship         0
race                 0
sex                  0
capital-gain         0
capital-loss         0
hours-per-week       0
native-country     583
income               0
dtype: int64
--------------------------------------------------------------------------------


totalWorkclass = 1836
totalOccupation = 1843
totalNativecountry = 583
totalWorkclass + totalOccupation + totalNativecountry = 4262

Es más del 10% de los datos de entrenamiento, por lo que no se eliminarán filas.
Se usará en cambio la imputación de valores por moda, ya que son variables categóricas.

## Preguntas Guía
### ¿Qué variables numéricas y categóricas consideraste para el análisis?
### Variables numéricas
- age
- education-num
- capital-gain
- capital-loss
- hours-per-week
### Variables categóricas
- workclass
- marital-status
- occupation
- relationship
- race
- sex
- native-country

### ¿Qué impacto tienen los valores faltantes en K-means?
K-means no puede trabajar con valores nulos.

### ¿Qué limitación supone ignorar las variables categóricas?
Ignorar las variables categóricas supone perderse de patrones que podrían determinar los grupos.

# 2. El script debe contener una implementación de la Distancia de Gower

## 1. Implementar la fórmula de Gower.
## 2. Validar con un subconjunto pequeño (ej. 5 filas).
## 3. Probar el cálculo de la matriz de distancias en una muestra pequeña del dataset.

## Preguntas guía:
### ¿Cómo se normalizan las variables numéricas antes de calcular la distancia?
Las variables numéricas se normalizan al calcular las distancias parciales.
Con la fórmula abs(columna1Registro1 - columna1Registro2) / Rango.
Por lo tanto, no es necesario normalizar los valores.

### ¿Por qué Gower es más adecuado que la distancia euclídeana en datos mixtos?
Es más adecuado en datos mixtos porque utiliza las variables categóricas y variables categóricas.

Si la columna es numérica:
- distanciaParcial = abs(columna1Registro1 - columna1Registro2) / Rango.

Si la columna es categórica:
- distanciaParcial = 1 si columna1Registro1 != columna1Registro2 sino 0.

Luego permite compararlas a través de:
- D_gower(registro1, registro2) = suma de distanciasParciales / cantidad de columnas

### ¿Qué problemas aparecen al calcular matrices de distancias muy grandes (de conjuntos de datos muy grandes)?
Con valores como por ejemplo: 32000 registros. Como en el caso del dataset(Adult)

- La matriz necesitaría 32.000*32.000 = 1.024.000.000 (más de mil millones) de elementos.
- Si cada distancia requiere 8 bytes, la matriz necesitaría aproximadamente 8GB de ram. Sólo para almacenarla.
- Para calcular esta matriz, tienes que comparar cada par de registros. Esto implica una complejidad de tiempo de O(N^2).
- Con N=32.000, el número de cálculos es inmenso, resultando en tiempos de ejecución muy largos.

# 3: El script debe dar tratamiento conjuntos de datos grandes
## 1. Tomar muestras de 5.000 y 10.000 registros.
## 2. Calcular la matriz de distancias Gower para cada muestra.
## 3. Medir tiempo de ejecución y uso de memoria.
## 4. Comparar estabilidad de clusters en muestras de distinto tamaño.
## Preguntas guía:
### ¿Qué diferencias observaste en tiempos de ejecución al aumentar el tamaño de la muestra?
Al aumentar el tamaño de la muestra, el tiempo de ejecucuón y el tamaño de memoria utilizado es sumamente mayor.
Con una muestra de 200 filas el tiempo de ejecución rondaba los 30 segundos con uso de memoria de 300KB.
Con una muestra de 5000 filas el tiempo de ejecución rondaba 2 hora y 20 minutos, con uso de memoria de 0.186GB.
Para terminar con una muestra de 10000 filas, utilizando un método paralelizado en 8 procesos, el tiempo de ejecución rondaba 2 horas y 41 minutos, con un uso de memoria de 0.745 GB.

### ¿Qué trade-off o compromiso hay entre usar todo el dataset y usar una muestra más pequeña?
Usar todo el dataset provee resultados más representativos y reales, incluye todas las observaciones y patrones. Incluso permite identificar outliers dentro de la muestra. 

Pero acarrea el problema de ser pesada de calcular en tiempo y memoria, al ser el 
algoritmo de O(n^2) en eficiencia de memoria y cálculo.

Además el tiempo de procesamiento puede llevar horas o días.

Usar una muestra permite que el cálculo sea más rápido y viable.
Pero existe el riesgo de que la muestra no sea representativa de la población.
Se puede perder información, los patrones raros o subpoblaciones pueden ser exluidos.

### ¿Qué técnicas se podrían aplicar en problemas reales para escalar este análisis a millones de registros?

1. Optimización del Cómputo (CPU/GPU)
Estas técnicas se enfocan en hacer el cálculo de la distancia por par lo más rápido posible:
- Vectorización Avanzada: Paquetes como gower en python utilizan c/c++.
- Parelización a Nivel de procesos: Módulos como multiprocessing permite paralelizar.
- Aceleración por hardware: Utilizar la GPU para el cálculo de matricez.

2. Reducción de la Complejidad (Muestreo y Algoritmos)
Estas técnicas evitan calcular la matriz NxN completa, reduciendo la complejidad efectiva:
- Muestreo Estratégico: Se utiliza una muestra altamente representativa.
- Algoritmos de Clustering Basados en Muestra: CLARA.
- Técnicas de Clustering de Baja Dimensionalidad: PCA

3. Computación Distribuida
Para datasets que no caben en la memoria RAM de una sola máquina (terabytes de datos), se utilizan frameworks de Big Data:
- Apache Spark: El cálculo de las distancias se distribuye a través del clúster de máquinas, y la matriz se ensambla de manera escalable.

# 4: El script debe realizar un agrupamiento o clustering con K-means
## 1. Seleccionar las variables numéricas del dataset.
## 2. Escalar si es necesario (normalizar)
## 3. Aplicar K-means con distintos valores de k.
## 4. Determinar el número óptimo de clusters (Silhouette y/o Davies-Bouldin).

## Preguntas guía:
### ¿Qué criterios usaste para definir el número de clusters?
Utilice el índice de silhouette para determinar el número de clusters
Además, también fue útil usar el Método del Codo (Elbow Method), basado en la innercia
(SSE), se usó como un criterio visual.

### ¿Qué patrones observaste en los clusters formados?



### ¿Qué limitaciones tiene K-means al ignorar las variables categóricas?