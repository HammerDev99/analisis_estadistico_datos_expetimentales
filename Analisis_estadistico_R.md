# Análisis estadístico de datos experimentales

Autor: Daniel Arbeláez

Fecha de Creación: 2023/06/09

## Tabla de contenido

- [Análisis estadístico de datos experimentales](#análisis-estadístico-de-datos-experimentales)
  - [Tabla de contenido](#tabla-de-contenido)
  - [Objetivo del proyecto](#objetivo-del-proyecto)
  - [Preparación de los datos](#preparación-de-los-datos)
  - [Estudio de los datos](#estudio-de-los-datos)
  - [Análisis de varianza (ANOVA) y prueba de rangos múltiples con (TUKEY)](#análisis-de-varianza-anova-y-prueba-de-rangos-múltiples-con-tukey)
    - [**ColonizacionHMA**](#colonizacionhma)
    - [**P\_Foliar**](#p_foliar)
    - [**PesoSeco**](#pesoseco)
  - [Graficación de los datos obtenidos ANOVA y TUKEY](#graficación-de-los-datos-obtenidos-anova-y-tukey)
  - [Análisis post-hoc: pruebas de separación de medias (Prueba Tukey para las medias por tratamiento)](#análisis-post-hoc-pruebas-de-separación-de-medias-prueba-tukey-para-las-medias-por-tratamiento)
  - [Conclusiones](#conclusiones)
  - [Referencias](#referencias)

## Objetivo del proyecto

Desarrollar un análisis estadístico de datos experimentales utilizando R y RStudio para manipulación y visualización avanzada de datos, aplicando técnicas de Análisis de Varianza (ANOVA) y Pruebas de Tukey para evaluar diferencias significativas entre grupos de tratamiento. La técnica ANOVA permite identificar y comparar variaciones en las medias de múltiples grupos, ideal para estudios con múltiples factores. La Prueba de Tukey, utilizada como prueba post-hoc, examina pares de medias para señalar diferencias específicas, proporcionando una interpretación detallada y visual de las variaciones entre tratamientos mediante ggplot.

## Preparación de los datos

Primero se prepara el dataset que se tiene en un archivo `CSV` y se importa a RStudio con la función readr

```r
library(readr)
datos_experimento <- read_delim("Downloads/datos_experimento.csv", delim = ";", escape_double = FALSE, trim_ws = TRUE)
View(datos_experimento)
```

En caso de requerir la conversión del tipo de dato de una columna usar los siguientes comandos

```r
datos_experimento$Tratamiento <- as.character(datos_experimento$Tratamiento)
datos_experimento$TratamientoRepeticion <- as.character(datos_experimento$TratamientoRepericion)
datos_experimento$ColonizacionHMA <- as.double(datos_experimento$ColonizacionHMA)
datos_experimento$P_Foliar <- as.double(datos_experimento$P_Foliar)
datos_experimento$PesoSeco <- as.double(datos_experimento$PesoSeco)
class(datos_experimento[[1]]) # Comando para ver el tipo de dato de la columna 1
```

Dataset:

![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%2011.50.16%20a. m..png)

El resumen de los datos:

![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%2012.02.49%20p. m..png)

## Estudio de los datos

- **Gráfica de medias por cada tratamiento**
  
  ```r
  plot(aggregate(datos_experimento$ColonizacionHMA ~ datos_experimento$Tratamiento, data = datos_experimento, FUN = mean))
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.23.05%20p. m..png)

  ```r
  plot(aggregate(datos_experimento$P_Foliar ~ datos_experimento$Tratamiento, data = datos_experimento, FUN = mean))
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.24.18%20p. m..png)

  ```r
  plot(aggregate(datos_experimento$PesoSeco ~ datos_experimento$Tratamiento, data = datos_experimento, FUN = mean))
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.25.30%20p. m..png)

- **Distribución de los datos de forma creciente**
  
  - ColonizacionHMA

    ```r
    ggplot(data = datos_experimento, aes(x = reorder(Tratamiento, +ColonizacionHMA), y = ColonizacionHMA, color = Tratamiento)) + geom_boxplot() + theme_bw()
    ```

    ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.38.02%20p. m..png)

  - P_Foliar

    ```r
    ggplot(data = datos_experimento, aes(x = reorder(Tratamiento, +P_Foliar), y = P_Foliar, color = Tratamiento)) + geom_boxplot() + theme_bw()
    ```

    ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.38.45%20p. m..png)

  - PesoSeco

    ```r
    ggplot(data = datos_experimento, aes(x = reorder(Tratamiento, +PesoSeco), y = PesoSeco, color = Tratamiento)) + geom_boxplot() + theme_bw()
    ```

    ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.39.31%20p. m..png)

- **Frecuencia de los datos**

```r
# Frecuencia de ColonizacionHMA
ggplot(datos_experimento, aes(x = datos_experimento$ColonizacionHMA)) + geom_histogram(bins = 20, color = "white", fill = "blue")
```

![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%203.30.25%20p. m..png)

```r
# Frecuencia de P_Foliar
ggplot(datos_experimento, aes(x = datos_experimento$P_Foliar)) + geom_histogram(bins = 20, color = "white", fill = "blue")
```

![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%203.33.49%20p. m..png)

```r
# Frecuencia de PesoSeco
ggplot(datos_experimento, aes(x = datos_experimento$PesoSeco)) + geom_histogram(bins = 20, color = "white", fill = "blue")
```

![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%203.34.42%20p. m..png)

- **Tabla de medias en orden ascendente**

  ```r
  #ColonizacionHMA
  # Creación de tabla de medias
  tabla_medias_col <- aggregate(datos_experimento$ColonizacionHMA ~ datos_experimento$Tratamiento, data = datos_experimento, FUN = mean)

  # Gráfica
  ggplot(tabla_medias_col, aes(x = reorder(`datos_experimento$Tratamiento`,`datos_experimento$ColonizacionHMA`), y = `datos_experimento$ColonizacionHMA`)) + geom_bar(stat="identity", color='black',fill='transparent') + theme_bw()
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.51.19%20p. m..png)

  ```r
  # P_Foliar
  # Creación de tabla de medias
  tabla_medias_pfo <- aggregate(datos_experimento$P_Foliar ~ datos_experimento$Tratamiento, data = datos_experimento, FUN = mean)

  # Gráfica
  ggplot(tabla_medias_pfo, aes(x = reorder(`datos_experimento$Tratamiento`,`datos_experimento$P_Foliar`), y = `datos_experimento$P_Foliar`)) + geom_bar(stat="identity", color='black',fill='transparent') + theme_bw()
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.49.41%20p. m..png)

  ```r
  # PesoSeco
  # Creación de tabla de medias
  tabla_medias_col <- aggregate(datos_experimento$PesoSeco ~ datos_experimento$Tratamiento, data = datos_experimento, FUN = mean)

  # Gráfica
  ggplot(tabla_medias_col, aes(x = reorder(`datos_experimento$Tratamiento`,`datos_experimento$PesoSeco`), y = `datos_experimento$PesoSeco`)) + geom_bar(stat="identity", color='black',fill='transparent') + theme_bw()
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.50.09%20p. m..png)

## Análisis de varianza (ANOVA) y prueba de rangos múltiples con (TUKEY)

Análisis de la Varianza ( ANOVA ) es una fórmula estadística que se utiliza para comparar las varianzas entre las medias (o el promedio) de diferentes grupos, en este caso los grupos son los diferentes tratamientos que van del 1 al 12 cada uno con 5 repeticiones. Además se deberá analizar la varianza para las columnas con los datos de ColonizacionHMA, P_Foliar y PesoSeco.

### **ColonizacionHMA**

- **ANOVA**

  ```r
  # Anova de todos los datos experimentales
  modelo_anova_col <- aov(datos_experimento$ColonizacionHMA ~ datos_experimento$Tratamiento)
  View(modelo_anova_col)
  ```

  Resultado:

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%2012.17.49%20p. m..png)

  ```r
  summary(modelo_anova_col)
  ```

  Resultado:

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%2012.18.16%20p. m..png)

- **TUKEY**

  ```r
  TukeyHSD(modelo_anova_col)
  ```

  Resultado:

  ```r
  Tukey multiple comparisons of means
      95% family-wise confidence level

  Fit: aov(formula = datos_experimento$ColonizacionHMA ~ datos_experimento$Tratamiento)

  $`datos_experimento$Tratamiento`
                diff         lwr         upr     p adj
  10-1  -0.626531548 -0.83877461 -0.41428848 0.0000000
  11-1  -0.627864881 -0.84010795 -0.41562182 0.0000000
  12-1  -0.620805957 -0.83304902 -0.40856289 0.0000000
  2-1    0.096922955 -0.11532011  0.30916602 0.9120240
  3-1    0.230772356  0.01852929  0.44301542 0.0224761
  4-1   -0.136548680 -0.34879174  0.07569438 0.5514398
  5-1    0.113707878 -0.09853519  0.32595094 0.7882824
  6-1   -0.015614056 -0.22785712  0.19662901 1.0000000
  7-1   -0.623454625 -0.83569769 -0.41121156 0.0000000
  8-1   -0.631252859 -0.84349592 -0.41900980 0.0000000
  9-1   -0.558060960 -0.77030402 -0.34581790 0.0000000
  11-10 -0.001333333 -0.21357640  0.21090973 1.0000000
  12-10  0.005725590 -0.20651747  0.21796865 1.0000000
  2-10   0.723454503  0.51121144  0.93569757 0.0000000
  3-10   0.857303904  0.64506084  1.06954697 0.0000000
  4-10   0.489982868  0.27773980  0.70222593 0.0000000
  5-10   0.740239426  0.52799636  0.95248249 0.0000000
  6-10   0.610917492  0.39867443  0.82316056 0.0000000
  7-10   0.003076923 -0.20916614  0.21531999 1.0000000
  8-10  -0.004721311 -0.21696438  0.20752175 1.0000000
  9-10   0.068470588 -0.14377248  0.28071365 0.9928735
  12-11  0.007058924 -0.20518414  0.21930199 1.0000000
  2-11   0.724787836  0.51254477  0.93703090 0.0000000
  3-11   0.858637238  0.64639417  1.07088030 0.0000000
  4-11   0.491316201  0.27907314  0.70355927 0.0000000
  5-11   0.741572759  0.52932970  0.95381582 0.0000000
  6-11   0.612250825  0.40000776  0.82449389 0.0000000
  7-11   0.004410256 -0.20783281  0.21665332 1.0000000
  8-11  -0.003387978 -0.21563104  0.20885509 1.0000000
  9-11   0.069803922 -0.14243914  0.28204699 0.9916582
  2-12   0.717728912  0.50548585  0.92997198 0.0000000
  3-12   0.851578314  0.63933525  1.06382138 0.0000000
  4-12   0.484257277  0.27201421  0.69650034 0.0000000
  5-12   0.734513835  0.52227077  0.94675690 0.0000000
  6-12   0.605191901  0.39294884  0.81743497 0.0000000
  7-12  -0.002648667 -0.21489173  0.20959440 1.0000000
  8-12  -0.010446902 -0.22268997  0.20179616 1.0000000
  9-12   0.062744998 -0.14949807  0.27498806 0.9965816
  3-2    0.133849402 -0.07839366  0.34609247 0.5809911
  4-2   -0.233471635 -0.44571470 -0.02122857 0.0199050
  5-2    0.016784923 -0.19545814  0.22902799 1.0000000
  6-2   -0.112537011 -0.32478007  0.09970605 0.7987698
  7-2   -0.720377580 -0.93262064 -0.50813452 0.0000000
  8-2   -0.728175814 -0.94041888 -0.51593275 0.0000000
  9-2   -0.654983914 -0.86722698 -0.44274085 0.0000000
  4-3   -0.367321036 -0.57956410 -0.15507797 0.0000187
  5-3   -0.117064478 -0.32930754  0.09517859 0.7569320
  6-3   -0.246386412 -0.45862948 -0.03414335 0.0109586
  7-3   -0.854226981 -1.06647005 -0.64198392 0.0000000
  8-3   -0.862025216 -1.07426828 -0.64978215 0.0000000
  9-3   -0.788833316 -1.00107638 -0.57659025 0.0000000
  5-4    0.250256558  0.03801349  0.46249962 0.0091210
  6-4    0.120934624 -0.09130844  0.33317769 0.7187012
  7-4   -0.486905945 -0.69914901 -0.27466288 0.0000000
  8-4   -0.494704179 -0.70694724 -0.28246112 0.0000000
  9-4   -0.421512280 -0.63375534 -0.20926922 0.0000009
  6-5   -0.129321934 -0.34156500  0.08292113 0.6303638
  7-5   -0.737162503 -0.94940557 -0.52491944 0.0000000
  8-5   -0.744960737 -0.95720380 -0.53271767 0.0000000
  9-5   -0.671768838 -0.88401190 -0.45952577 0.0000000
  7-6   -0.607840569 -0.82008363 -0.39559750 0.0000000
  8-6   -0.615638803 -0.82788187 -0.40339574 0.0000000
  9-6   -0.542446904 -0.75468997 -0.33020384 0.0000000
  8-7   -0.007798235 -0.22004130  0.20444483 1.0000000
  9-7    0.065393665 -0.14684940  0.27763673 0.9951387
  9-8    0.073191900 -0.13905116  0.28543496 0.9878135
  ```

### **P_Foliar**

- **ANOVA**

  ```r
  modelo_anova_pfoliar <- aov(datos_experimento$P_Foliar ~ datos_experimento$Tratamiento)
  View(modelo_anova_pfoliar)
  ```

  Resultado:

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%201.47.41%20p. m..png)

  ```r
  summary(modelo_anova_pfoliar)
  ```

  Resultado:

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%201.48.28%20p. m..png)

- **TUKEY**

  ```r
  TukeyHSD(modelo_anova_pfoliar)
  ```

  Resultado:

  ```r
  Tukey multiple comparisons of means
      95% family-wise confidence level

  Fit: aov(formula = datos_experimento$P_Foliar ~ datos_experimento$Tratamiento)

  $`datos_experimento$Tratamiento`
                diff           lwr          upr     p adj
  10-1   8.550936e-05 -2.766041e-05 1.986791e-04 0.3111891
  11-1   5.807826e-05 -5.509151e-05 1.712480e-04 0.8295371
  12-1   7.850970e-05 -3.466007e-05 1.916795e-04 0.4370178
  2-1    7.226676e-05 -4.090301e-05 1.854365e-04 0.5625657
  3-1    9.553590e-05 -1.763387e-05 2.087057e-04 0.1740690
  4-1    2.743110e-05 -8.573867e-05 1.406009e-04 0.9994258
  5-1    9.004968e-05 -2.312009e-05 2.032194e-04 0.2423480
  6-1    1.206968e-04  7.527074e-06 2.338666e-04 0.0273425
  7-1    9.477918e-05 -1.839059e-05 2.079489e-04 0.1825240
  8-1    8.475264e-05 -2.841713e-05 1.979224e-04 0.3237132
  9-1    1.119946e-04 -1.175206e-06 2.251643e-04 0.0547517
  11-10 -2.743110e-05 -1.406009e-04 8.573867e-05 0.9994258
  12-10 -6.999660e-06 -1.201694e-04 1.061701e-04 1.0000000
  2-10  -1.324260e-05 -1.264124e-04 9.992717e-05 0.9999996
  3-10   1.002654e-05 -1.031432e-04 1.231963e-04 1.0000000
  4-10  -5.807826e-05 -1.712480e-04 5.509151e-05 0.8295371
  5-10   4.540320e-06 -1.086294e-04 1.177101e-04 1.0000000
  6-10   3.518748e-05 -7.798229e-05 1.483572e-04 0.9947520
  7-10   9.269820e-06 -1.038999e-04 1.224396e-04 1.0000000
  8-10  -7.567200e-07 -1.139265e-04 1.124130e-04 1.0000000
  9-10   2.648520e-05 -8.668457e-05 1.396550e-04 0.9995868
  12-11  2.043144e-05 -9.273833e-05 1.336012e-04 0.9999672
  2-11   1.418850e-05 -9.898127e-05 1.273583e-04 0.9999992
  3-11   3.745764e-05 -7.571213e-05 1.506274e-04 0.9912168
  4-11  -3.064716e-05 -1.438169e-04 8.252261e-05 0.9984175
  5-11   3.197142e-05 -8.119835e-05 1.451412e-04 0.9976968
  6-11   6.261858e-05 -5.055119e-05 1.757883e-04 0.7533396
  7-11   3.670092e-05 -7.646885e-05 1.498707e-04 0.9925606
  8-11   2.667438e-05 -8.649539e-05 1.398441e-04 0.9995582
  9-11   5.391630e-05 -5.925347e-05 1.670861e-04 0.8868028
  2-12  -6.242940e-06 -1.194127e-04 1.069268e-04 1.0000000
  3-12   1.702620e-05 -9.614357e-05 1.301960e-04 0.9999949
  4-12  -5.107860e-05 -1.642484e-04 6.209117e-05 0.9181355
  5-12   1.153998e-05 -1.016298e-04 1.247097e-04 0.9999999
  6-12   4.218714e-05 -7.098263e-05 1.553569e-04 0.9778675
  7-12   1.626948e-05 -9.690029e-05 1.294392e-04 0.9999968
  8-12   6.242940e-06 -1.069268e-04 1.194127e-04 1.0000000
  9-12   3.348486e-05 -7.968491e-05 1.466546e-04 0.9965563
  3-2    2.326914e-05 -8.990063e-05 1.364389e-04 0.9998809
  4-2   -4.483566e-05 -1.580054e-04 6.833411e-05 0.9655639
  5-2    1.778292e-05 -9.538685e-05 1.309527e-04 0.9999920
  6-2    4.843008e-05 -6.473969e-05 1.615998e-04 0.9417167
  7-2    2.251242e-05 -9.065735e-05 1.356822e-04 0.9999138
  8-2    1.248588e-05 -1.006839e-04 1.256556e-04 0.9999998
  9-2    3.972780e-05 -7.344197e-05 1.528976e-04 0.9859904
  4-3   -6.810480e-05 -1.812746e-04 4.506497e-05 0.6475792
  5-3   -5.486220e-06 -1.186560e-04 1.076835e-04 1.0000000
  6-3    2.516094e-05 -8.800883e-05 1.383307e-04 0.9997462
  7-3   -7.567200e-07 -1.139265e-04 1.124130e-04 1.0000000
  8-3   -1.078326e-05 -1.239530e-04 1.023865e-04 1.0000000
  9-3    1.645866e-05 -9.671111e-05 1.296284e-04 0.9999964
  5-4    6.261858e-05 -5.055119e-05 1.757883e-04 0.7533396
  6-4    9.326574e-05 -1.990403e-05 2.064355e-04 0.2003482
  7-4    6.734808e-05 -4.582169e-05 1.805178e-04 0.6627638
  8-4    5.732154e-05 -5.584823e-05 1.704913e-04 0.8409122
  9-4    8.456346e-05 -2.860631e-05 1.977332e-04 0.3268891
  6-5    3.064716e-05 -8.252261e-05 1.438169e-04 0.9984175
  7-5    4.729500e-06 -1.084403e-04 1.178993e-04 1.0000000
  8-5   -5.297040e-06 -1.184668e-04 1.078727e-04 1.0000000
  9-5    2.194488e-05 -9.122489e-05 1.351146e-04 0.9999330
  7-6   -2.591766e-05 -1.390874e-04 8.725211e-05 0.9996634
  8-6   -3.594420e-05 -1.491140e-04 7.722557e-05 0.9937336
  9-6   -8.702280e-06 -1.218720e-04 1.044675e-04 1.0000000
  8-7   -1.002654e-05 -1.231963e-04 1.031432e-04 1.0000000
  9-7    1.721538e-05 -9.595439e-05 1.303851e-04 0.9999943
  9-8    2.724192e-05 -8.592785e-05 1.404117e-04 0.9994617
  ```

### **PesoSeco**

- **ANOVA**

  ```r
  modelo_anova_pesoseco <- aov(datos_experimento$PesoSeco ~ datos_experimento$Tratamiento)
  View(modelo_anova_pesoseco)
  ```

  Resultado:

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%201.52.49%20p. m..png)

  ```r
  summary(modelo_anova_pesoseco)
  ```

  Resultado:

  ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%201.53.51%20p. m..png)

- **TUKEY**

  ```r
  TukeyHSD(modelo_anova_pesoseco)
  ```

  Resultado:

  ```r
  Tukey multiple comparisons of means
      95% family-wise confidence level

  Fit: aov(formula = datos_experimento$PesoSeco ~ datos_experimento$Tratamiento)

  $`datos_experimento$Tratamiento`
                diff         lwr        upr     p adj
  10-1  -2.000000e-02 -0.11705987 0.07705987 0.9998783
  11-1  -4.000000e-03 -0.10105987 0.09305987 1.0000000
  12-1   1.400000e-02 -0.08305987 0.11105987 0.9999967
  2-1   -1.000000e-02 -0.10705987 0.08705987 0.9999999
  3-1    3.200000e-02 -0.06505987 0.12905987 0.9914908
  4-1    4.400000e-02 -0.05305987 0.14105987 0.9158880
  5-1    1.000000e-02 -0.08705987 0.10705987 0.9999999
  6-1   -1.000000e-02 -0.10705987 0.08705987 0.9999999
  7-1    3.400000e-02 -0.06305987 0.13105987 0.9862208
  8-1    6.000000e-03 -0.09105987 0.10305987 1.0000000
  9-1    2.498002e-16 -0.09705987 0.09705987 1.0000000
  11-10  1.600000e-02 -0.08105987 0.11305987 0.9999869
  12-10  3.400000e-02 -0.06305987 0.13105987 0.9862208
  2-10   1.000000e-02 -0.08705987 0.10705987 0.9999999
  3-10   5.200000e-02 -0.04505987 0.14905987 0.7882667
  4-10   6.400000e-02 -0.03305987 0.16105987 0.5143870
  5-10   3.000000e-02 -0.06705987 0.12705987 0.9950070
  6-10   1.000000e-02 -0.08705987 0.10705987 0.9999999
  7-10   5.400000e-02 -0.04305987 0.15105987 0.7470705
  8-10   2.600000e-02 -0.07105987 0.12305987 0.9985647
  9-10   2.000000e-02 -0.07705987 0.11705987 0.9998783
  12-11  1.800000e-02 -0.07905987 0.11505987 0.9999570
  2-11  -6.000000e-03 -0.10305987 0.09105987 1.0000000
  3-11   3.600000e-02 -0.06105987 0.13305987 0.9786832
  4-11   4.800000e-02 -0.04905987 0.14505987 0.8602256
  5-11   1.400000e-02 -0.08305987 0.11105987 0.9999967
  6-11  -6.000000e-03 -0.10305987 0.09105987 1.0000000
  7-11   3.800000e-02 -0.05905987 0.13505987 0.9683480
  8-11   1.000000e-02 -0.08705987 0.10705987 0.9999999
  9-11   4.000000e-03 -0.09305987 0.10105987 1.0000000
  2-12  -2.400000e-02 -0.12105987 0.07305987 0.9993089
  3-12   1.800000e-02 -0.07905987 0.11505987 0.9999570
  4-12   3.000000e-02 -0.06705987 0.12705987 0.9950070
  5-12  -4.000000e-03 -0.10105987 0.09305987 1.0000000
  6-12  -2.400000e-02 -0.12105987 0.07305987 0.9993089
  7-12   2.000000e-02 -0.07705987 0.11705987 0.9998783
  8-12  -8.000000e-03 -0.10505987 0.08905987 1.0000000
  9-12  -1.400000e-02 -0.11105987 0.08305987 0.9999967
  3-2    4.200000e-02 -0.05505987 0.13905987 0.9373325
  4-2    5.400000e-02 -0.04305987 0.15105987 0.7470705
  5-2    2.000000e-02 -0.07705987 0.11705987 0.9998783
  6-2    5.551115e-17 -0.09705987 0.09705987 1.0000000
  7-2    4.400000e-02 -0.05305987 0.14105987 0.9158880
  8-2    1.600000e-02 -0.08105987 0.11305987 0.9999869
  9-2    1.000000e-02 -0.08705987 0.10705987 0.9999999
  4-3    1.200000e-02 -0.08505987 0.10905987 0.9999993
  5-3   -2.200000e-02 -0.11905987 0.07505987 0.9996949
  6-3   -4.200000e-02 -0.13905987 0.05505987 0.9373325
  7-3    2.000000e-03 -0.09505987 0.09905987 1.0000000
  8-3   -2.600000e-02 -0.12305987 0.07105987 0.9985647
  9-3   -3.200000e-02 -0.12905987 0.06505987 0.9914908
  5-4   -3.400000e-02 -0.13105987 0.06305987 0.9862208
  6-4   -5.400000e-02 -0.15105987 0.04305987 0.7470705
  7-4   -1.000000e-02 -0.10705987 0.08705987 0.9999999
  8-4   -3.800000e-02 -0.13505987 0.05905987 0.9683480
  9-4   -4.400000e-02 -0.14105987 0.05305987 0.9158880
  6-5   -2.000000e-02 -0.11705987 0.07705987 0.9998783
  7-5    2.400000e-02 -0.07305987 0.12105987 0.9993089
  8-5   -4.000000e-03 -0.10105987 0.09305987 1.0000000
  9-5   -1.000000e-02 -0.10705987 0.08705987 0.9999999
  7-6    4.400000e-02 -0.05305987 0.14105987 0.9158880
  8-6    1.600000e-02 -0.08105987 0.11305987 0.9999869
  9-6    1.000000e-02 -0.08705987 0.10705987 0.9999999
  8-7   -2.800000e-02 -0.12505987 0.06905987 0.9972341
  9-7   -3.400000e-02 -0.13105987 0.06305987 0.9862208
  9-8   -6.000000e-03 -0.10305987 0.09105987 1.0000000
  ```

## Graficación de los datos obtenidos ANOVA y TUKEY

- **ColonizacionHMA**
  
  ```r
  plot(modelo_anova_col)
  ```

  Resultado:

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.06.23%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.07.04%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.07.31%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.07.59%20p. m..png)

  ```r
  plot(TukeyHSD(modelo_anova_col))
  ```
  
  Resultado:

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.09.46%20p. m..png)

- **P_Foliar**

  ```r
  plot(modelo_anova_pfoliar)
  ```

  Resultado:

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.11.31%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.12.02%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.12.24%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.12.46%20p. m..png)

  ```r
  plot(TukeyHSD(modelo_anova_pfoliar))
  ```
  
  Resultado:

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.13.19%20p. m..png)

- **PesoSeco**

  ```r
  plot(modelo_anova_pesoseco)
  ```

  Resultado:

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.14.45%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.15.14%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.15.41%20p. m..png)

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.16.02%20p. m..png)

  ```r
  plot(TukeyHSD(modelo_anova_pesoseco))
  ```
  
  Resultado:

    ![alt](src/Captura%20de%20Pantalla%202023-06-09%20a%20la(s)%202.16.27%20p. m..png)

## Análisis post-hoc: pruebas de separación de medias (Prueba Tukey para las medias por tratamiento)

- Instalar y usar las librerias siguientes

  ```r
  library(datasets)
  library(ggplot2)
  library(multcompView)
  library(dplyr)
  ```

- Validar que los modelos de anova (analisis de varianza) estan creados con la formula en el [Análisis de varianza](#análisis-de-varianza-anova-y-prueba-de-rangos-múltiples-con-tukey) previo

  ```r
  summary(modelo_anova_col)
  summary(modelo_anova_pfoliar)
  ```

- Crear variable tukey (comparación de medias) para cada modelo de anova existente

  ```r
  tukey_col <- TukeyHSD(modelo_anova_col)
  print(tukey_col) # Para ver los datos del modelo
  tukey_pfo <- TukeyHSD(modelo_anova_pfoliar)
  print(tukey_pfo) # Para ver los datos del modelo
  ```

- Usar la visualización de letras compactas (compact letter display) para simplificar la visualización y el análisis de las diferencias significativas entre las medias calculadas

  ```r
  cld_col <- multcompLetters4(modelo_anova_col, tukey_col)
  cld_pfo <- multcompLetters4(modelo_anova_pfoliar, tukey_pfo)
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-10%20a%20la(s)%201.02.55%20p. m..png)

- Creación de tabla con los datos resumidos y las letras

  - Tabla con factores y tercer cuartil
  
  ```r
  # Colonizacion
  Tk_col <- group_by(datos_experimento, datos_experimento$Tratamiento) %>% summarise(mean=mean(datos_experimento$ColonizacionHMA), quant = quantile(datos_experimento$ColonizacionHMA, probs = 0.75)) %>% arrange(desc(mean))
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%206.26.33%20p. m..png)

  ```r
  # P_Foliar
  Tk_pfo <- group_by(datos_experimento, datos_experimento$Tratamiento) %>% summarise(mean=mean(datos_experimento$P_Foliar), quant = quantile(datos_experimento$P_Foliar, probs = 0.75)) %>% arrange(desc(mean))
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%206.27.02%20p. m..png)

  - Obtener letras y agregar a la tabla creada

  ```r
  # Colonizacion
  cld_col <- as.data.frame.list(cld_col$`datos_experimento$Tratamiento`)
  Tk_col$cld_col <- cld_col$Letters
  print(Tk_col)
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-10%20a%20la(s)%201.26.19%20p. m..png)

  ```r
  # P_Foliar
  cld_pfo <- as.data.frame.list(cld_col$`datos_experimento$Tratamiento`)
  Tk_pfo$cld_pfo <- cld_pfo$Letters
  print(Tk_pfo)
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%206.31.58%20p. m..png)

- Boxplots coloreados y **ordenados de forma creciente** según el factor (variable explicativa)

  ```R
  # Colonización
  ggplot(datos_experimento, aes(x = reorder(Tratamiento, +ColonizacionHMA), y = ColonizacionHMA)) + geom_boxplot(aes(fill = Tratamiento)) + labs(x="Tipo de tratamiento", y="Colonizacion (HMA)") + theme_bw() + theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank()) + geom_text(data = Tk_col, aes(x = `datos_experimento$Tratamiento`, y = quant, label = cld_col), size = 4)
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.34.19%20p. m..png)

  ```R
  # P_Foliar
  ggplot(datos_experimento, aes(x = reorder(Tratamiento, +P_Foliar), y = P_Foliar)) + geom_boxplot(aes(fill = Tratamiento), position = "dodge") + labs(x="Tipo de tratamiento", y="P_Foliar") + theme_bw() + theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank()) + geom_text(data = Tk_pfo, aes(x = `datos_experimento$Tratamiento`, y = quant, label = cld_pfo), size = 4)
  ```

  ![alt](src/Captura%20de%20Pantalla%202023-06-11%20a%20la(s)%207.34.48%20p. m..png)

## Conclusiones

1. **Identificación de diferencias significativas**: El análisis de varianza (ANOVA) permitió comparar las medias de distintos grupos de tratamiento, revelando diferencias significativas en variables específicas. Esto es esencial para determinar el efecto de los tratamientos en las variables estudiadas.

2. **Evaluación detallada de tratamientos**: La aplicación de la Prueba de Tukey como análisis post-hoc permitió identificar qué grupos presentaban diferencias significativas entre sí. Este paso fue crucial para señalar, con un mayor grado de precisión, los tratamientos que tienen efectos distintos.

3. **Visualización de datos para una interpretación clara**: Las herramientas de visualización como ggplot fueron clave para presentar los resultados de manera gráfica y comprensible, facilitando la interpretación de la variabilidad y las diferencias encontradas entre los grupos.

4. **Confianza en la robustez del análisis**: La combinación de técnicas como ANOVA y Prueba de Tukey proporcionó un marco estadístico robusto, asegurando que los resultados y conclusiones fueran estadísticamente significativos y replicables, apoyando futuras decisiones basadas en estos hallazgos.

## Referencias

[ANOVA análisis de varianza para comparar múltiples medias](https://rpubs.com/Joaquin_AR/219148)

[Iniciación a la visualización de datos ggplot](hhttps://www.uv.es/vcoll/Temas_AED/4_Visualizacion_datos.html)

[Gráfico de barras](https://bookdown.org/jboscomendoza/r-principiantes4/graficas-de-barras.html)

[Reordering a ggplot bar chart axis](https://www.rpubs.com/dvdunne/reorder_ggplot_barchart_axis)

[ggplot2](https://rpubs.com/hllinas/R_Barras_ggplot_univariada)

[ANOVA UNIDIRECCIONAL Y DIAGRAMA DE CAJA EN R CON LETRAS (CLD)](https://statdoe.com/one-way-anova-and-box-plot-in-r/)

[Introduction to ggridges (multiples curvas en gráfico)](https://cran.r-project.org/web/packages/ggridges/vignettes/introduction.html)

[Estimated Marginal Means for Multiple Comparisons](https://rcompanion.org/handbook/G_06.html)

[Compact Letter Display (CLD)](https://schmidtpaul.github.io/DSFAIR/compactletterdisplay.html)