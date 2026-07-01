
# Resumen del Análisis de Datos Meteorológicos Australianos

## 1. Presentación del Dataset
Este dataset presenta datos de 10 años de observaciones climáticas de muchas locaciones en Australia. El análisis de este dataset fue realizado con el propósito de predecir si el día siguiente lloverá o no en los lugares de estudio dentro de Australia.

### 1.1. Objetivo del Análisis
El objetivo de este análisis es crear un modelo predictivo que permita saber si lloverá al día siguiente.

### 1.2. Desafíos del Dataset
Los desafíos principales del dataset incluyen:
1) Datos faltantes en varias variables numéricas.
2) Posible estacionalidad.
3) Diferencias entre ubicaciones geográficas.
4) Tamaño grande, lo que puede ralentizar el remuestreo del modelo.

## 2. Descripción de las Columnas del Dataset
El dataset contiene las siguientes columnas:
- **Date** — Fecha de la observación.
- **Location** — Ciudad o estación meteorológica.
- **MinTemp** — Temperatura mínima del día (°C).
- **MaxTemp** — Temperatura máxima del día (°C).
- **Rainfall** — Cantidad de lluvia (mm).
- **Evaporation** — Evaporación medida (mm).
- **Sunshine** — Horas de sol.
- **WindGustDir** — Dirección de la ráfaga de viento más fuerte.
- **WindGustSpeed** — Velocidad de la ráfaga más fuerte (km/h).
- **WindDir9am** — Dirección del viento a las 9am.
- **WindDir3pm** — Dirección del viento a las 3pm.
- **WindSpeed9am** — Velocidad del viento a las 9am (km/h).
- **WindSpeed3pm** — Velocidad del viento a las 3pm (km/h).
- **Humidity9am** — Humedad relativa a las 9am (%).
- **Humidity3pm** — Humedad relativa a las 3pm (%).
- **Pressure9am** — Presión atmosférica a las 9am (hPa).
- **Pressure3pm** — Presión atmosférica a las 3pm (hPa).
- **Cloud9am** — Cobertura de nubes a las 9am (octas).
- **Cloud3pm** — Cobertura de nubes a las 3pm (octas).
- **Temp9am** — Temperatura a las 9am (°C).
- **Temp3pm** — Temperatura a las 3pm (°C).
- **RainToday** — Si llovió hoy (Yes/No).
- **RainTomorrow** — Variable objetivo: si lloverá mañana (Yes/No).

## 3. Exploración Inicial del Dataset

### Información General
El número total de datos consta de 145460, correspondiente a las columnas de Date y Location. El resto de las columnas presentan menos datos que las mencionadas anteriormente. La columna Date se muestra de tipo Object, la cual fue transformada a `datetime64[ns]`.

### Datos Faltantes
1) Se creó una matriz de datos faltantes que contiene los nulos del dataset.
2) Se verificó si existía al menos un dato faltante por columna.
3) Se contó el número de datos faltantes por columna.
4) Se contó el número de datos faltantes total del dataset.

El porcentaje total de valores nulos en el dataset es de 10.26%. Dada la cantidad de datos faltantes, se revisaron los métodos de imputación. Inicialmente, es probable que existan columnas con outliers que estén deformando la estadística de los datos.

No existen filas duplicadas en el dataset.

## 4. Estrategias de Imputación de Valores Faltantes

Para decidir la mejor estrategia de imputación, se consideró el porcentaje de valores nulos y el tipo de dato de cada columna para minimizar la alteración de la distribución estadística.

1.  **Variables Numéricas con bajo/moderado porcentaje de nulos (ej. < 10-15%):** Se utilizó la mediana cuando la diferencia porcentual entre la media y la mediana fue superior al 20%, y la media en caso contrario.

2.  **Variables Categóricas con cualquier porcentaje de nulos:** Se imputaron con la moda, rellenando con el valor más frecuente.

3.  **Variables con alto porcentaje de nulos (ej. > 30-40% como `Sunshine`, `Evaporation`, `Cloud3pm`, `Cloud9am`):** Estas columnas fueron eliminadas del dataset para evitar introducir sesgos significativos debido a la gran cantidad de datos faltantes.

Después de la imputación y eliminación de columnas, el DataFrame quedó completamente sin valores nulos en las columnas restantes.

## 5. Medidas de Dispersión y Forma de la Distribución

Se calcularon medidas de dispersión (desviación estándar, varianza, coeficiente de variación, rango, rango intercuartílico) y medidas de forma (asimetría y curtosis) para las columnas numéricas.

Algunos hallazgos importantes incluyen:
- En 'Rainfall', el coeficiente de variación es muy alto (363.51%), indicando una gran dispersión relativa a su media.
- En 'Pressure9am' y 'Pressure3pm', el coeficiente de variación es muy bajo (0.66%), mostrando mucha consistencia.
- Muchas columnas (`MinTemp`, `MaxTemp`, `Pressure9am`, `Pressure3pm`, `Temp9am`, `Temp3pm`, `Humidity9am`, `Humidity3pm`) muestran distribuciones aproximadamente simétricas (asimetría < 0.5).
- Columnas como `Rainfall`, `WindGustSpeed`, `WindSpeed9am`, `WindSpeed3pm` presentan asimetría positiva, con colas hacia la derecha.

## 6. Intervalos de Confianza para la Media

El análisis de intervalos de confianza al 95% sobre 145.460 observaciones meteorológicas australianas revela estimaciones de alta precisión en todas las variables, con márgenes de error inferiores a ±0.15 en todos los casos. Esto es consecuencia directa del gran tamaño muestral que garantiza la plena aplicabilidad del Teorema del Límite Central.

Los datos evidencian un ciclo diurno atmosférico coherente y consistente:
- La temperatura asciende aproximadamente 4.7°C entre las 9am y las 3pm (16.99°C → 21.68°C).
- La velocidad del viento aumenta cerca de 4.6 km/h en la tarde (14.04 → 18.66 km/h).
- La humedad relativa cae alrededor de 17 puntos porcentuales (68.88% → 51.54%).
- La presión atmosférica desciende levemente unas 2.4 hPa (1017.65 → 1015.26 hPa).
Todo ello es físicamente consistente con los procesos de calentamiento y convección diurna.

En cuanto a la variabilidad:
- La presión atmosférica es la variable más estable del conjunto (CV < 1%).
- Las temperaturas y velocidades de viento presentan dispersión moderada.
- La precipitación (Rainfall) constituye el caso atípico más relevante del análisis: con una media de apenas 2.31 mm pero una desviación estándar de 8.39 mm, su coeficiente de variación supera el 363%. Esto indica una distribución fuertemente asimétrica a la derecha donde la media no representa el comportamiento típico y el IC técnicamente correcto [2.26, 2.35] debe interpretarse con cautela. Para esta variable se recomienda complementar el análisis con la mediana o aplicar una transformación logarítmica antes de modelar.

En síntesis, el dataset demuestra alta calidad estadística y confiabilidad para inferencia poblacional en once de las doce variables, con la precipitación como excepción que requiere tratamiento diferenciado.
