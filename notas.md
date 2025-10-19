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