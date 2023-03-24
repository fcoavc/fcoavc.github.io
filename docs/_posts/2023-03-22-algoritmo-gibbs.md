---
layout: post
title:  "Algoritmo Gibbs sampler"
date:   2023-03-22 12:00:00 -0600
categories: inferencia simulación 
tags: algoritmo bayesiano inferencia sas r julia python 
author: Francisco Ariel
---

{% include footer2.html %}

En estadística Bayesiana es de interés conocer la distribución _a posteriori_, es decir:

$$
f(\theta|x) \propto f(X|\theta)f(\theta)
$$

donde $\mathbf{\theta}$ es un vector de parámetros de dimensión $k$ y $x$ representa los datos.

Sin embargo, en ocasiones obtener esta densidad es compilcada, por lo que se deben recurrir a técnicas numéricas.

Una de estas técnicas es el _algoritmo de Gibbs_ o _muestrador de Gibbs_ (Hoff 2009). Este método en un algoritmo iterativo que consiste en obtener muestras de las distribuciones condicionales completas de la siguiente manera:

Dada una suceción de valores $\mathbf{\theta}^{(i)}=(\theta_1^{(i)},\theta_2^{(i)},\dots,\theta_k^{(i)})^t$ se simula de la siguiente manera:

1.- Simular de las distribuciones condicionales completas:

$$
\begin{align*}
\theta_1^{(i+1)} &\sim f(\theta_1|x,\theta_2^{(i)},\dots,\theta_k^{(i)})\\
\theta_2^{(i+1)} &\sim f(\theta_2|x,\theta_1^{(i+1)},\theta_3^{(i)}\dots,\theta_k^{(i)})\\
\vdots\\
\theta_k^{(i+1)} &\sim f(\theta_k|x,\theta_1^{(i+1)},\theta_2^{(i+1)}\dots,\theta_{k-1}^{(i+1)})
\end{align*}
$$

2.- Repetir desde el paso 1 hasta obtener convergencia.

Note que este algoritmo requiere especificar cada una de las distribuciones condicioneales completas y se deben especificar valores iniciales y algún criterio de paro.

## Ejemplo de aplicación

El siguiente ejemplo muestra el funcionamiento del algoritmo _Gibb sampler_ para el caso de la regresión lineal simple, se pueden consultar más detalles en este [enlace](https://franciscoariel.github.io/site/estadistica/bayesiano/#modelo-de-regresion-lineal-simple).

La base de datos utilizada para el ejemplo fue obtenida de [kaggle](https://www.kaggle.com/datasets/rkiattisak/salaly-prediction-for-beginer) y consiste en información de salarios de cierta compañia en función de años de experiencia, nivel de educación género y grado de estudios, para fines ilustrativos, únicamente se tomará en cuenta la variable años de experiencia, la base puede descargarse de este [link](/assets/SalaryData.csv).


### Ejemplo con R

Primero se puede estimar los parámetros simulando de la distibución a posteriori.

{% highlight r %}
# Datos
datos = read.csv("https://fcoavc.github.io/docs/site/assets/SalaryData.csv")

{% endhighlight %}