---
layout: post
title:  "Optimización de funciones con Newton-Raphson"
date:   2023-02-04 12:00:00 -0600
categories: inferencia optimización 
tags: optimización verosimilitud weibull sas r julia python 
author: Francisco Ariel
---

{% include footer2.html %}

En inferencia estadística es de interés conocer el Estimador de Máxima Verosimilitud, es decir, un estimador que maximice la función de verosimilitud. Sin embargo, salvo en pocos casos, no es posible encontrar un estimador de forma analítica, por lo que es necesario recurrir a métodos numéricos. Una forma de encontrar una estimación que maximice la función de verosimilitud es mediante el algoritmo Newton-Raphson.

El método de Newton-Raphson es una técnica numérica para encontrar las raíces de una función (Burden & Faires, 2002), es decir, los valores de $x$ tal que $f(x)=0$. La idea básica es aproximarse "actualizando" los valores obtenidos anteriormente hasta encontrar una solución aproximada, si es posible hallarla.

La fórmula para encontrar la raíz de la función es la siguiente:

$$
x^{(i) }=x^{(i−1) }−\frac{f\left(x^{(i−1) }\right)}{f´\left(x^{(i−1) } \right) }
$$

El algoritmo se describe a continuación.

**Datos de entrada**: valor inicial `x0`, tolerancia `tol`, número de iteraciones `niter`.

1.- Tomar `i=1`

2.- Mientras `i<=niter` hacer

  3.-     Calcular `x=x0-f(x0)/f’(x0)` 

  4.-     Si `|x-x0|<tol` entonces regresa `x` y finaliza satisfactoriamente.

  5.-     Tomar `i=i+1`

  6.-     Tomar `x0=x`

7.- Fin Mientras

8.- Salida. El método no logró encontrar una solución.

9.- Fin del programa.

Se requiere de un valor inicial $x^{(0)}$ para iniciar con la búsqueda y que la función $f(x)$ sea derivable.

Una ventaja importante de este algoritmo es que puede aplicarse a funciones multivariadas.

## Implementación del algoritmo

En muchas aplicaciones estadísticas, se requiere encontrar el valor $\hat{\theta}$ que _maximice_ el logaritmo de la verosimilitud $l(\theta)$, por lo que los valores de $\hat{\theta}$ se encuentran mediante la búsqueda de las raíces de la derivada de la log-verosimilitud o **función score** $S(\theta)$ y con el negativo de la segunda derivada $H(\theta)$ o **Hessiano**. Note que en este caso se requiere que la función de log-verosimilitud sea _2 veces derivable_ con respecto a $\theta$.

La fórmula para encontrar una estimación del vector de parámetros que maximiza la función de log-varosimilitud es la siguiente.

$$
\hat{\theta}^{(i) }=\hat{\theta}^{(i−1) }+H\left(\hat{\theta}^{(i−1) }\right)^{-1} S\left(\hat{\theta}^{(i−1) } \right) 
$$

El método de Newton-Raphson no solo sirve para encontrar estimaciones puntuales de los parámetros, sino que es posible obtener una estimación de la varianza de los estimadores usando la inversa del negativo del hessiano o también llamada **Información de Fisher**, por lo que este método es útil para construir un intervalo de confianza usando las propiedades del estimador de máxima verosimilitud.

## Ejemplo de aplicación

Los datos para el siguiente ejemplo fueron tomados de Meeker y Escobar (1998). Supóngase que se tienen datos de un experimento en el cual se registró el tiempo de falla hasta las 5 mil horas de cierto dispositivo a distintas temperaturas. Para este ejercicio, se omitirán los tiempos con censura y únicamente se considerarán observaciones cuya temperatura fue de 80 °C.

Si se asume que los tiempos de falla $T_1,\dots, T_n$ se ajustan a una distribución Weibull, su función de distribución está dada por

$$f_T(t;\gamma,\beta) = \frac{\gamma}{\beta}\left( \frac{t}{\beta}\right) ^{\gamma-1}e^{-\left( \frac{t}{\beta}\right)^\gamma }\;t,\beta ,\gamma > 0$$

Es de interés estimar los parámetros $\gamma$ y $\beta$, donde $\gamma$ es el parámetro de forma y $\beta$ es el parámetro de escala.

Puede mostrarse que la función de log-verosimilitud es la siguiente:

$$l(\gamma,\beta) = n (\log \gamma - \gamma \log \beta) + (\gamma - 1) \sum_{i=1}^{n} \log t_i -  \frac{\sum_{i=1}^{n}t_i^\gamma}{\beta^\gamma}$$

Para utilizar el algoritmo Newton-Raphson, se requiere encontrar la función Score.

$$
S(\gamma,\beta)=\begin{pmatrix}
\frac{\partial l(\gamma,\beta)}{\partial \beta}\\
\frac{\partial l(\gamma,\beta)}{\partial \gamma} 
\end{pmatrix}
$$

donde

$$
\frac{\partial l(\gamma,\beta)}{\partial \beta} = \frac{-n\gamma}{\beta} +  \gamma \left( \frac{1}{\beta}\right)^{\gamma + 1} \sum_{i=1}^{n}t_i^\gamma
$$

y 

$$
\frac{\partial l(\gamma,\beta)}{\partial \gamma} = n \left(\frac{1}{\gamma} - \log \beta\right) + \sum_{i=1}^n \log t_i - \frac{\sum_{i=1}^{n}t_i^\gamma \log t_i + \log\frac{1}{\beta} \sum_{i=1}^{n}t_i^\gamma}{\beta ^{\gamma}}  
$$

Así como el Hessiano

$$
H(\gamma,\beta) =
\begin{pmatrix}
    \frac{\partial^2 l(\gamma,\beta)}{\partial \beta^2} & \frac{\partial^2 l(\gamma,\beta)}{\partial \beta \partial \gamma} \\
    \frac{\partial^2 l(\gamma,\beta)}{\partial \beta \partial \gamma} &  \frac{\partial^2 l(\gamma,\beta)}{\partial \gamma^2}
\end{pmatrix}
$$

donde 

$$\frac{\partial^2 l(\gamma,\beta)}{\partial \beta^2} = \frac{n\gamma}{\beta^2} - \gamma(\gamma + 1) \left( \frac{1}{\beta}\right)^{\gamma + 2} \sum_{i=1}^{n}t_i^\gamma$$

, 

$$\frac{\partial^2 l(\gamma,\beta)}{\partial \beta \partial \gamma} =- \frac{n}{\beta} +\frac{\gamma \sum_{i=1}^{n}t_i^\gamma \log (t_i) + \left( 1+\gamma \log \frac{1}{\beta} \right)  \sum_{i=1}^{n}t_i^\gamma}{\beta ^{\gamma+1}}$$ 

y 

$$\frac{\partial^2 l(\gamma,\beta)}{\partial \gamma^2} = -\frac{n}{\gamma^2} - \frac{\sum_{i=1}^{n}t_i^\gamma \log (t_i)^2 + 2 \log\frac{1}{\beta} \sum_{i=1}^{n}t_i^\gamma\log t_i + \log \left( \frac{1}{\beta}\right)^2 \sum_{i=1}^{n}t_i^\gamma}{\beta ^{\gamma}} $$

Finalmente la matriz de covarianzas puede ser estimada con $V(\gamma,\beta) = H(\gamma,\beta)^{-1}$.

A continuación se mostrará cómo implementar este algoritmo en distintos lenguajes de programación.

### Ejemplo con R

Primero se puede definir 3 funciones que serán de utilidad, la primera será la función de log-verosimilitud, la segunda la función score y por último la función hessiana.

{% highlight r %}
# Datos
ti = c(283,361,515,638,854,1024,1030,1045,1767,1777,1856,1951,1964,2884)

# Función de log-verosimilitud
logv = function(params){
  g = params[1]
  b = params[2]
  n = length(ti)
  lv = n*(log(g)-g*log(b)) + (g-1)*sum(log(ti))-sum(ti^g)/b^g
  return(lv)
}

# Función score

{% endhighlight %}

## Referencias

Burden, R., & Faires, D. (2002). _Análisis numérico_ (Séptima Edición). Thomson Learning.

Meeker, W. Q. & Escobar, L. A. (1998), _Statistical Methods for Reliability Data_, New York, NY; John Wiley & Sons.

R Core Team. 2022. _R: A language and environment for statistical computing_. R Foundation for Statistical Computing. https://www.R-project.org/.

SAS Institute Inc. 2018. _SAS/STAT 15.1 User’s Guide_. Cary, NC.

----

> SAS and all other SAS Institute Inc. product or service names are registered trademarks or trademarks of SAS Institute Inc. in the USA and other countries. ® indicates USA registration.
