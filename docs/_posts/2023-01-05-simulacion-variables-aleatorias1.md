---
layout: post
title:  "Simulación de variables aleatorias"
date:   2023-01-05 12:00:00 -0600
categories: probabilidad simulación
tags: simulación variable aleatoria uniforme probabilidad sas r  
author: Francisco Ariel
---

{% include footer2.html %}

En probabilidad, el concepto de _variable aleatoria_ es uno de los pilares fundamentales para entender el comportamiento de un fenómeno aleatorio ya que su comportamiento está gobernado por una función de distribución $$F_X(x)$$.

Sin embargo en ocasiones es complicado visualizar el comportamiento de dicha variable aleatoria, por lo que es necesario __simular__ valores que podría tomar una realización de dicha variable aleatoria.

En este artículo se presenta una forma de simular valores de una distribución usando números aleatorios.

## Método para simular variables aleatorias

La técnica para simular variables aleatorias es la _transformación integral de probabilidad_. Esta técnica nos dice que para generar valores de una variable aleatoria $$X$$ con función de de distribución $$F_X(x)$$, es suficiente con generar valores aleatorios $$u$$ de una función de distribución uniforme en el intervalo (0,1) y hacer $$x = F_X^{-1}(u)$$. Las computadoras (e incluso calculadoras) tienen generadores de números aleatorios que pueden servir para crear valores de $$x$$.

Para mayores detalles se puede consultar (Mood, Graybill, y Boes 1974).

## Ejemplo de números aleatorios

Supóngase que se quieren simular valores de una distribución exponencial con parámetro $$\lambda = 5$$, es decir $$F_X(x)=(1-e^{-\lambda x})I_{(0,\infty)}(x)$$. Entonces para generar valores de esta distribución, basta con hacer $$x = -(1/\lambda)\log (1-U)$$.

### Ejemplo con R

El siguiente código en R muestra la forma de realizarlo. La forma de generar los números aleatorios es mediante la función `aleat_exp()`. Esta función usa un generados de números aleatorios uniformes y el resultado se almacena en el vector `x`. Posteriormente se crea el objeto `simulacion` para poder graficar su histograma.

{% highlight r %}
#Función para simular
aleat_exp <- function(n,l=1) {
  u = runif(n)
  x = (-1/l)*log(1-u)
  return(x)
}
x = aleat_exp(1000,5)
simulacion = data.frame(X =x)
#Gráfica
library(ggplot2)
ggplot(data = simulacion,aes(X))+geom_histogram()+theme_classic()+
  labs(y="Conteo",title = "Simulación de una variable aleatoria exponencial")
{% endhighlight %}

![Histograma de la simulación con R](/assets/sim_r.png)

### Ejemplo con SAS software

Se ejemplifica la forma de crear valores aleatorios usando SAS/STAT® software. Primero se definen las variables macro a usar `&n.` y `&lambda.`, posteriormente se usa un paso data para generar los números aleatorios y almacenarlos en un dataset y finalmente se grafican.

{% highlight sas %}
/*Variables macro*/
%LET n = 1000;
%LET lambda = 5;
data simulacion;
    drop i semilla;
    semilla = 123;
    do i = 1 to &n.;
       call ranuni(semilla,u);
       x = (-1/&lambda.)*log(1-u);
       output;
    end;
run;
/*Gráfico*/
proc sgplot data=simulacion;
histogram x;
run;
{% endhighlight %}

![Histograma de la simulación con SAS](/assets/sim_sas.png)

## Conclusión

Esta técnica es muy útil para simular valores con cierta distribución y es muy fácil de usar. Sin embargo, no siempre será fácil encontrar $$F_X^{-1}(u)$$ y se requerirán técnicas más avanzadas.

En el próximo artículo exploraremos otras técnicas para simular variables aleatorias de otras distribuciones, por ejemplo la distribución normal.

## Referencias

Mood, Alexander, Franklin Graybill, y Duane Boes. 1974. _Introduction to the Theory of Statistics_. McGraw-Hill.

R Core Team. 2022. _R: A language and environment for statistical computing_. R Foundation for Statistical Computing. https://www.R-project.org/.

SAS Institute Inc. 2018. _SAS/STAT 15.1 User’s Guide_. Cary, NC.

----

> SAS and all other SAS Institute Inc. product or service names are registered trademarks or trademarks of SAS Institute Inc. in the USA and other countries. ® indicates USA registration.
