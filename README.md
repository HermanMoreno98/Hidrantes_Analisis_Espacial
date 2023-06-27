# Metodología de cálculo de distancia por vecino más cercano entre puntos geográficos y su aplicación en el sector saneamiento.

## **1. Objetivo**

El objetivo del presente informe es presentar una metodología para el cálculo de distancias y áreas de puntos con ubicación geográfica y su aplicación en actividades relacionadas al saneamiento.

## **2. Metodología**

### **2.1. Primera parte**

Existen 3 algoritmos de vecinos más cercanos dentro de la librería \textit{scikit-learn}, los cuales son Ball Tree, KD Tree y Brute Force, y se detallan a continuación:

* La metodología de Brute Force se refiere al cálculo de distancias entre todos los pares de puntos en el conjunto de datos para $N$ muestras en $D$ dimensiones. Es importante mencionar que es un algoritmo ineficiente ante un gran tamaño de datos.
* La metodología de KD Tree se refiere a una estructura de árbol binario que divide recursivamente el espacio de parámetros a lo largo de los ejes de datos. Sin embargo, es ineficiente en grandes dimensiones de datos.
* La metodología de Ball Tree se refiere a la división recursiva de los datos en nodos anidados definidos por un centroide $C$ y un radio $r$, tal que cada punto en el nodo se encuentra dentro de la hiperesfera definida por $r$ y $C$. Este algoritmo mejora la ineficiencia de la alta dimensionalidad de los datos de KD Tree.

|Algoritmo|Medicion de distancia|
|---------|---------------------|
|KD Tree|euclidean, l2, minkowski, p, manhattan, cityblock, l1, chebyshev, infinity|
|Ball Tree|euclidean, l2, minkowski, p, manhattan, cityblock, l1, chebyshev, infinity, seuclidean, mahalanobis, wminkowski, hamming, canberra, braycurtis, matching, jaccard, dice, kulsinski, rogerstanimoto, russellrao, sokalmichener, sokalsneath, haversine, pyfunc|


El algoritmo de Ball Tree se puede contemplar como un árbol métrico. Los árboles métricos organizan y estructuran los puntos de datos teniendo en cuenta el espacio métrico en el que se encuentran los puntos. 

$$
semiversin\left(\frac{d}{R}\right) = semiversine(\phi_1 - \phi_2) + \cos(\phi_1) \cos(\phi_2) \cdot semiversine(\Delta\lambda)
$$

$$
d = 2R \arcsin\left(\sqrt{\sin^2\left(\frac{\phi_2 - \phi_1}{2}\right) + \cos(\phi_1) \cos(\phi_2) \sin^2\left(\frac{\Delta\lambda}{2}\right)}\right)
$$

Donde:
* $semiversine$ es la función semiverso, $semiversine(\theta) = \sin^2\left(\frac{\theta}{2}\right) = \frac{1 - \cos(\theta)}{2}$
* $d$ es la distancia entre dos puntos (sobre un círculo máximo de la esfera)
* $R$ es el radio de la esfera
* $\phi_1$ es la latitud del punto 1
* $\phi_2$ es la latitud del punto 2
* $\Delta\lambda$ es la diferencia de longitudes


La ecuación anterior se puede resolver mediante la librería haversine y scikit-learn.
En el presente documento se utiliza la segunda librería, ya que dentro del cálculo de vecino más cercano por Ball-Tree, existe una implementación de distancia.
Para ello se siguen los siguientes pasos:

* Convertir las coordenadas geográficas (latitud y longitud) a radianes.
* Pasar como input dichas coordenadas a la función de distancia.
* Finalmente, se debe convertir las distancias calculadas a las unidades deseadas (metros, kilómetros, etc).

En el último se debe tener en cuenta que, para convertir la distancia en radianes a metros (que será la unidad de medida para los resultados), se debe multiplicar dicha distancia por el radio de la tierra en metros. Usando el radio medio de la tierra y tomando en cuenta el elipsoide WGS84, el valor de aproximadamente 6371.009 kilómetros o 6371000.009 metros.

### **2.2. Segunda parte**

El área cubierta por punto geográfico con un radio determinado en metros se calcula de la siguiente manera:

$$
    AT = \frac{[(A_{p1} + A_{p2}) - (A_{p12} + A_{p13})]}{A_{s1} + A_{s2}}
$$

Donde:

* $AT$ = Area total  cubierta
* $A_{s1}$ = Area del sector 1
* $A_{s2}$ = Area del sector 2
* $A_{p1}$ = Área del polígono 1
* $A_{p2}$ = Área del polígono 2
* $A_{p12}$ = Área del polígono 12
* $A_{p13}$ = Área del polígono 13
