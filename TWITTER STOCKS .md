---
title: "APLICACION DE LA METODOLOGIA BOX-JENKINS NO ESTACIONAL PARA MODELAR UN ARIMA Y PRONOSTICAR EL PRECIO DE LAS ACCIONES DE TWITTER"
author: "Marco Percastre"
date: "26 de mayo de 2022"
output:
  prettydoc::html_pretty:
    theme: cayman
    highlight: github
---

```{css my-header-colors, echo = FALSE}
.page-header {
    background-image: linear-gradient(120deg, black, blue);
}
```

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
library(DT)
library(dplyr)
library(lmtest)
library(ggstatsplot)
library(ggplot2)
library(readr)
library(tseries)
library(forecast)
library(lmtest)
library(fUnitRoots)
library(tidyverse)
library(lubridate)
library(astsa)
library(foreign)
library(quantmod)
```

<ul lista 1><h1 align=right ><b style= 'color: darkblue; font-size:30px'>Integrantes:</b></h1>
<li style='color:black'>Arturo Balam Miranda Hernandez</li>
<li style='color:black'>Luis Abraham Garcia Figueroa</li>
<li style='color:black'>Marco Antonio Percastre Sotelo</li>
</ul>


#TEMA

<ul lista 1><h1 align=center ><b style= 'color: darkblue; font-size:40px'>APLICACION DE LA METODOLOGIA BOX-JENKINS NO ESTACIONAL PARA MODELAR UN ARIMA Y PRONOSTICAR EL PRECIO DE LAS ACCIONES DE TWITTER.</b></h1></ul>

#INTRODUCCION

Se pretender mostrar una herramienta estadistica de analisis univariante para la prediccion de una empresa que cotice en un mercado continuo y el precio objetivo de la compañia, es decir, su accion. La aplicacion de este modelo es util para valoracion de acciones y empresas, asi como la prediccion de volatilidad. Su unica desventaja es que al ser un modelo que toma en cuenta una unica variable no existe posibilidad de relacionarla con mas variables que afectan al valor de las acciones de una empresa, aun asi, este modelo es demasiado util cuendo se quiere una prediccion a corto plazo debido a su aplicacion estadistica, lo que lo hace un modelo muy util para realizar predicciones de valores bursatiles. 

La diferencia entre proceso y modelo. Un proceso es lo real, del cual se desconoce su mecanismo generador. Un modelo es la imitacion o representacion del modelo (Pankratz 1983).

Los procesos se clasifican en estacionarios y no estacionarios. Los proceso estacionarios no presentan cambios sistematicos en la media ni en la varianza, es decir, son constantes con el tiempo, con esto se dice que un proceso estacionario es estable, miestras que, por otro lado, los procesos no estacionarios si presentan cambios en la media y la varianza, con esto se dice que la serie es inestable en el tiempo.

Los modelo $ARIMA$: se puede aplicar con datos discretos (numeros enteros) y continuos (numeros fraccionarios); necesitan de un numero minimo de 50 observaciones (Box-Jenkins); se caracterizan porque su ACF decrece muy lentamente; se asume que las perturbaciones aleatorias presentes en la serie son independientes entre si, no existe ninguna correlacion y por lo tanto ningun patron modelable; es un modelo desarrollado con combinaciones de proceso Autoregresivos (AR), Integrados (I) y Media Movil (MA).

$ACF$, llamada como Funcion de Autocorrelacion, mide la correlacion entre dos variables separadas por $k$ periodos y mide el grado de asociacion lineal que existe entre dos variables del mismo proceso estocastico. Identifica el proceso Media Movil $MA$ y nos da informacion de su orden $q$.

$PACF$, llamada como Funcion de Autocorrelacion Parcial, mide la correlacion entre dos variables separadas por $k$ periodos cuando no se considera la dependencia creada por los retardos intermedios existentes entre ambas, y mide la autocorrelacion que existe entre dos variables $k$ periodos descontando los posibles efectos debido a variables intermedias. Identifica valores del proceso Autoregresivo $AR$ y nos da informacion de su orden $p$.

$DFA$, llamada Dickey-Fuller Aumentada, es una prueba estadistica de raices unitarias, permite detectar la existencia o no existencia de estas raices unitarias y determina el comportamiento del proceso estocastico.

$Akaike$,  nos permite identificar un modelo de manera mas sencilla y automatica. Es el Criterio de Informacion Akaike, la solucion dada es elegir como funcion de perdida (Criterios de Especificacion) el minimo de criterio de informacion AIC. Permite interpretar la distancia entre dos distribuciones a partir de la log-verosimilitud de un modelo. Es hallar una funcion perdida que al ser minimizada permita obtener el modelo que mejor se ajusta a los datos.  

Metodologia Box-Jenkins No estacional, es la metodologia mas usada y conocida propuesta por G.E.P. Box y G.M. Jenkins en la decada de 1970 en su obra $Time Series Analisis: Forecasting and Control$, utilizada para la prediccion de variables economicas. Esto con el motivo identificar, modificar y ajustar los modelos ARIMA adecuados debido a que durante ese periodo, en el que habia estanflacion, dejaba sin capacidad explicativa a los modelos econometricos de caracter estructural. Esta metodologia se basa en 5 etapas principales:
1. Estacionariedad
2. Identificacion
3. Estimacion 
4. Evaluacion
5. Pronostico

#TABLA DE PRECIOS DE LAS ACCIONES DE TWITTER

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
library(readr)
TWTR <- read.csv("~/6to SEMESTRE/TEORIA ECONOMETRICA II R STUDIO/PROYECTO FINAL/TWTR (2).csv")

TWTR %>% datatable(class = 'cell-border stripe',
                     caption = 'Nombre de la tabla',
                     colnames = c('','Date','Open','High','Low','Close','Adj Close','Volume'))
```

#INFORMACION DE LA VARIABLE A UTILIZAR

De la tabla anterior, la variable utilizada para este proyecto recoge el precio de cierre de cada dia en los ultimos dos años, del 17 de mayo del 2020 al 17 de mayo del 2022. El tamaño muestral de la serie debe ser igual o mayor a 50 observaciones, como se indico anteriormente, para este proyecto el total de observaciones es de 505, por lo que nos lanzara resultados muy efectivos del precio en dolares. Los datos del precio fueron obtenedos de $finance.yahoo.com$ y corresponde a las acciones de Twitter. 


#GRAFICA DE LA SERIE TEMPORAL, 
Aqui seguimos el punto uno de Estacionariedad de la metodologia.

```{r echo=FALSE,warning=FALSE, cache=FALSE, message=FALSE, fig.align='center'}
X_t <- TWTR$Close
plot(X_t, type = 'l', 
     col = 'blue',
     lwd = 0.1,
     main = 'TWTR-Twitter,Inc',
     xlab = 'tiempo',
     ylab = 'valor',
     cex.main = 1.5,
     col.main = '#1E90FF',
     font.main = 4,
     font.lab = 3)
```

Asi a simple vista podemos ver que el proceso es no estacionario, es decir, es inestable, debido a que cuando se llega a una apreciacion no fluctua en torno a una media y lo mismo ocurre durante todo el comportamiento de la serie de tiempo. Para estar mas seguros de si la serie es estacionaria o no, vamos a realizar la $ACF$, $PACF$ y la prueba estadistica de $DFA$.

ACF:
```{r echo=FALSE,warning=FALSE, cache=FALSE, message=FALSE, fig.align='center'}
acf(X_t, col = 'red', 
    font.lab = 3,
    main = 'ACF:TWTR-Twitter,Inc',
    cex.main = 1.5,
    font.main = 4)

pacf(X_t, col = 'red', 
    font.lab = 3,
    main = 'PACF:TWTR-Twitter,Inc',
    cex.main = 1.5,
    font.main = 4)[1]
```

Podemos verlo claramente, el resultado del proceso en el $ACF$ y $PACF$ es no estacionario, esto se observa en los respectivos grafico. La $ACF$ decrece con una lentitud extrema sin llegar a tener un valor a $0$, por lo que los valores de la serie temporal no son estacionarios, y la $PACF$ presenta un valor muy proximo a 1 de $0.987$ que es indicativo de la precencia de raices unitarias. Por lo que ahora vamos a realizar la prueba de $DFA$ para confirmarlo estadisticamente.

PRUEBA DE DFA: vamos a presentar la siguiente prueba de hipotesis.

<p align='center'>$H_0$ No estacionaria y con raices unitarias $\rho=$ 0</p>
<p align='center'>$H_1$ Estacionaria sin raices unitarias $\rho<$ 0</p>

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
adf.test(X_t) #prueba de Dickey-Fuller Aumentada
```

haciendo la prueba estadistica de $DFA$ nos saco un $p-value=0.7231$ y dado que tenemos una $Sig.=0.05$ se observa que $p-value>Sig.$, es decir, $0.7231>0.05$, entonces decimos que tenemos evidencia estadistica para aceptar la $H_0:$ que nos dice que es no estacionario y que tiene raices unitarias. Vemos tambien que tiene un orden de retraso igual a 7. 

Otra forma de ver si es estacionario o no estacionario:
```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
urkpssTest(X_t, type = c("tau"), lags = c("short"),use.lag = NULL, doplot = TRUE)
```

NOTA: la realidad es que las series de tiempo normalmente no se comportan establemente, es decir, no son procesos estocasticos estacionarios.

Y dado que tenemos un proceso estocastico no estacionario y la precencia de raices unitarias procedemos a la diferenciacion con la funcion $diff()$ de R y veamos como se ve graficamente:

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
X_tsta <- diff(X_t, differences=1)
plot(X_tsta, type = 'l', 
     xlab = 'tiempo', 
     ylab = 'valor-sta', 
     main = 'TWTR-Close-sta', 
     col = 'blue', 
     col.main = '#1E90FF', 
     cex.main = 1.5, 
     font.main = 4, 
     font.lab = 3)

```

la funcion $ndiffs()$ nos permitira encontrar el numero de primeras diferencias necesarias para que la serie de tiempo sea estacionaria, para este caso en particular, nos dice que solo es $1$ el numero de diferencias para hacer que nuestro proceso sea estacionario, como se ve a continuacion:

```{r}
ndiffs(X_t) #determinar el número de primeras diferencias necesarias para que las series temporales no sean estacionales
```

La integracion que es el proceso de estabilizacion y diferenciacion, por medio del parametro $d$ dentro del modelo $ARIMA(p,d,q)$. Para nuestro proceso de serie de tiempo es $d=1$.

Veamos la $ACF$ y $PACF$ de la serie con estacionariedad aplicando la diferenciacion: $(TWTR-Close-Sta)$.

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
acf(X_tsta, col = 'red', 
    font.lab = 3,
    main = 'ACF:TWTR-Close-Sta',
    cex.main = 1.5,
    font.main = 4)

pacf(X_tsta, col = 'red', 
    font.lab = 3,
    main = 'PACF:TWTR-Close-Sta',
    cex.main = 1.5,
    font.main = 4)[1]
```

Aplicando la otra forma de ver si es estacionario o no estacionario:

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
urkpssTest(X_tsta, type = c("tau"), lags = c("short"),use.lag = NULL, doplot = TRUE)
```

logramos ver que ya es estacionaria, es decir, es estable nuestra nueva serie $(TWTR-Close-Sta)$, porque podemos observar como la serie ya estaria pasa por una media constante igual a 0. Una vez que logramos la estacionaridad ya podemos realizar la modelacion $ARIMA$, por lo que ahora vamos a identificar el modelo mas probable que mejor se adapta.

Al hacer las $ACF$ y la $PACF$ obtenimos los posibles candidatos para el modelo, y con esto hicimos el punto dos de la metodologia que es la Identificacion.

#CRITERIO DE INFORMACION AKAIKE (AIC)

Aqui es el punto tres de la metodologia, la Estimacion, sacando la $ACF$ y $PACF$ de la serie ya estacionaria y estable $(TWTR-Close-Sta)$ podemos identificar los posibles candidatos para nuetro modelo $ARIMA(p,d,q)$. Los posibles candidatos son $p=3$, $d=1$ y $q=1$, asi que nuestro modelo quedaria de la forma $ARIMA(3,1,1)$. Cabe mencionar que $d=1$ porque fue el numero de diferenciacion para suavizar y hacer estacionaria y estable nuestro proceso.

NOTA: los posibles candidatos se aplican a la serie de tiempo del proceso inicial.

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
#ARIMA(p,d,q), Criterio de Informacion de Akaike (AIC)
P <- 3
D <- 1
Q <- 1

tabla <- data.frame()
for(p in 0:P) for(d in 0:D) for(q in 0:Q){
  modelo <- arima(X_t, order = c(p,d,q))
  A <- AIC(modelo)
  tabla <- rbind(tabla, c(p,d,q,A))
}

tabla <- rename(tabla, 'p'=1,'d'=2,'q'=3,'AIC'=4)
tabla <- tabla[order(tabla$AIC),] #ordena la tabla para constuir la tabla 
tabla

#plot(tabla$AIC)
```

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
min(tabla$AIC)
```

El mejor candidato para el modelo $(ARIMA(p,d,q)$ es aquel que tiene el menor valor de Criterio de Informacion Akaike (AIC), para este caso, el mejor cadidatos esta de la forma $(ARIMA(2,1,1)$ con un valor de $AIC=1925.100$.

Realizamos la modelacion para el mejor candidatos que es un $ARIMA(2,1,1)$ con nuestra serie de tiempo inicial:

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
modelo1 <- arima(X_t, order = c(2,1,1))
summary(modelo1)
```

#SIGNIFICANCIA DE LOS COEFICIENTES

Los parametros $\theta$ y $\rho$ del modelo propuesto deben cumplir con los siguientes requerimientos: 
1. Un modelo con parsimonia ajusta la informacion disponible sin usar coeficientes innecesarios.
2. El modelo debe ser estacionario e inversible.
3. Los coeficientes del modelo deben ser estadisticamente significativos.

procedemos a obtener la significancia de los coeficientes del modelo, para saberlo es necesario realizar los intervalos de confianza para los coeficientes de $ar1$, $ar2$ y $ma1$. Con un intervalo de confianza del 0.95 y una $Sig.=0.05$.Los coeficientes son los siguientes: 
$ar1=-0.5320$
$ar2=0.1409$
$ma1=0.5653$

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
confint(modelo1, 1 ,0.95)
confint(modelo1, 2 ,0.95)
confint(modelo1, 3 ,0.95)
```

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
confint(modelo1)
```

Construyendo los intervalo de confianza podemos observar que los coeficientes para $ar1$, $ar2$ y $m1$ se encuentran dentro del los intervalos de confianza por lo que decimos que son significativos.

4. El modelo debe proporcionar un adecuado ajuste.

#LA ECUACION DEL MODELO QUEDA DE LA SIGUIENTE EXPRESION:
La ecuacion del modelo esta de la forma:
$(1-\alpha_{1}B-\alpha_{2}B^{2}-...-\alpha_{p}B^{p})(1-B)^{d}X_{t}=c+(1+\theta_{1}B+\theta_{2}B^{2}+...+\theta_{q}B^{q})W_{t}$

donde B es el operador rezago: $BX_{t}=X_{t-1}$, $B^{2}X_{t}=X_{t-2}$, ... , $B^{n}X_{t}=X_{t-n}$

La ecuacion de nuestro modelo quedo de la forma:
$(1-(-0.5320B)-0.1409B^{2})X_{t}=(1+0.5653)W_{t}$
$(1+0.5320B-0.1409B^{2})X_{t}=(1+0.5653)W_{t}$
$X_{t}+0.5320X_{t}-0.1409B^{2}X_{t}=0.5653W_{t}+W_{t}$
$X_{t}=-0.5320BX_{t}+0.1409B^{2}X_{t}+0.5653W_{t}+W_{t}$
$$X_{t}=-0.5320X_{t-1}+0.1409X_{t-2}+0.5653W_{t-1}+W_{t}$$

#GRAFICA DE LOS RESIDUOS DEL MODELO 
Aqui es el punto cuatro de la metodologia, la Evaluacion. Un modelo estadisticamente adecuado es aquel cuyos residuales son independientes entre si, es decir, los residuales son completamente aleatorios (4.Modelación ARIMA, UNAM).

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
urkpssTest(modelo1$residuals, type = c("tau"), lags = c("short"),use.lag = NULL, doplot = TRUE)
```

Los graficos indican visualmente que no hay correlacion entre los residuos y que se distribuyen de forma independiente. Para estar seguro de esto, hay que verificarlo estadisticamente con una prueba de Ljung-Box.

#PRUEBA DE LJUNG-BOX

Pero antes se hay que hacer un diagnostico para saber si el modelo es bueno:
```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
tsdiag(modelo1)
```

Aqui destaca el garfico de los valores p para el estadistico de Ljung-Box que nos ayuda a identificar si hay o no ruido blanco. Esta es una manera grafica de esta prueba, la linea cortada azul tiene un valor igual a 0.05, mientras que los puntos son los valores de p, visualmente vemos que hay ruido blanco en el modelo y que este se ajusta bien. Para verificarlo haremos la prueba estadistica, lo cuales son las siguientes hipotesis:

<p align='center'>$H_0$ Hay Ruido Blanco y el modelo se ajusta bien $p valor >$ 0.05</p>
<p align='center'>$H_1$ No hay Ruido Blanco y el modelo no se ajusta bien $\rho <$ 0</p>

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
Box.test(residuals(modelo1), type = 'Ljung-Box')
```

Podemos ver que el valor de p de esta prueba es 0.9969 mucho mayor que 0.05, es decir, $0.9969>0.005$,esto significa que aceptamos la $H_{0}$ y que el modelo tiene Ruido Blanco y se ajusta bien.  

Veamos graficamente que el error tiene media igual a 0:

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
error <- residuals(modelo1)
plot(error, col = 'blue',
     main = 'MEDIA DEL ERROR',
     col.main = '#1E90FF',
     cex.main = 1.5,
     font.main = 4,
     xlab = 'tiempo',
     font.lab = 3)
```


#PRONOSTICO
Es el punto 5 de la metologia, aqui se quiere hacer un pronostico del valor y comportamiento de los siguientes 10 dias del cierre de las acciones de $TWTR-Twitter,Inc$ en el mercado financiero:

```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
futurVal <- forecast(modelo1, h = 10)
futurVal
plot(futurVal, col = 'blue',
     main = 'Pre-TWTR-Twitter,Inc',
     col.main = '#1E90FF',
     xlab = 'tiempo',
     ylab = 'valor',
     font.main = 4,
     cex.main = 1.5,
     font.lab = 3)
```

La grafica significa que existe un 80 por ciento de probabilidad de que los valores futuros del modelo esten dentro del area oscura sombreada y el 95 por ciento de probabilidad de que esten en el area clara sombreada.

#RAICES DEL MODELO
Graficamente asi se ven la raices:
```{r echo=FALSE, warning=FALSE, cache=FALSE, message=FALSE}
autoplot(modelo1)
```

Si las raices estan afuera (o muy cerca) del circulo unitario, el modelo sera numericamente inestable y no sera adecuado para hacer un pronostico. Para nosotros, podemos ver las reices unitarias se encuentran dentro del circulo unitario, por lo que podemos decir que es adecuado para hacer pronosticos. 

#RESUMEN

Es importante tener en cuenta que debe haber estacionariedad en la serie de tiempo para realizar un $ARIMA$, por lo que es fundamental utilizar la diferenciacion en el proceso original para suavizar el proceso no estacionario y asi poder analizar de major manera la serie de tiempo. Se recomienta seguir la metodologia de las 5 etapas de Box-Jenkins para modelar un ARIMA adecuado que consisten en: 1) Estacionariedad; 2) Identificacion; 3) Estimacion; 4) Evaluacion; y 5) Pronostico. La metodologia fue hecha por George E.P. Box y Gwilyn M. Jenkins, quienes se interesaron en estudiar las series temporales porque se dieron cuenta de la importancia que tenia en la economia y en los negocios, su principal enfoque fue solucionar los problemas de perturbaciones, por lo que dio lugar a la publicacion de su obra en 1970 titulada $Time Series Analysis: Forecasting and Control$, los modelos ARIMA tienen aplicacion en los negocios, economia, medicina y en cualquier otra ciencia, por lo que si te interesa conocer mas sobre las series temporales te recomendamos este libro debido a que tiene referencias invaluables para estadisticos aplicados, ingenieros y especialistas financieros. 

En este trabajo aplicamos la metodologia en relacion con lo aprendido durante el curso, por lo que aplicar y comprender la metodologia puede dar mayor sencillez al conocer las series de tiempo y, asi mismo, ser mas util en la modelacion de un ARIMA univariante.

Con lo que respecta al desarrollo del trabajo, debemos mencionar que la prediccion que se estima es aplicable para predecir el valor y comportamiento de la serie a corto plazo debido a su sustento estadistico. Las herramientas estadisticas aplicadas durante las etapas de la metodologia dan importancia y seguimiento para realizar una modelacion adecuada y asi realizar predicciones.

#REFERENCIAS

(s.f.). Procesos Integrados. En C. 5.

A Unexpected Route to Time Series. (30 de OCTOBER de 1989). This Weeks Citation Classic, pág. 1.

Campos, F. (2 de Noviembre de 2018). Analisis de series temporales en R. ARIMA. DIEGO CALVO.

Hernandez, S. (2015). Los modelos ARIMA univariantes . Analisis de Series de Tiempo (pags. 1-32). Mexico: CEPAL.

Lopez, A. M. (2011). ESTUDIO DEL AIC Y BIC EN LA SELECCIÓN DE MODELOS DE VIDA CON DATOS CENSURADOS. Guanajuato, Gto.: CIMAT.

Martinez, E. C. (14 de Mayo de 2019). EJERCICIO 6 Y 8. RPubs by Rstudio.

Mauricio, J. A. (2007). Análisi de Series Temporales. SERIES TEMPORALES (pags. 1-132). Madrid, España: Universidad Complutense de Madrid.

Modelacion ARIMA. (s.f.). 41-78.

PRELIMINAR, L. U. (2007). A. B. Iglesias, J. T. Pruñunosa y N. M. Llavines. Escuela Universitaria Caixa Terrassa , 1-13.


