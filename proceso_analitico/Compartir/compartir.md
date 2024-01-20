# Compartir: Resultados del Análisis del Conjunto de datos de Cyclistic

Alfredo Aburto
2024-01-16

## Objetivo

En el presente documento se presenta el análisis de los resultados que obtuvimos
en la fase anterior, se describe la interpretación de la información obtenida y
su importancia en el contexto de identificar las diferencias entre los usuario
`member` y los usuarios `casual` de forma detallada.

## Introducción

En este punto del proyecto considero pertinente hacer un recuento de los objetivos
que nos planteamos al inicio de este. Se nos planteó la situación de un sistema
de bicicletas que cuenta con dos tipos de usuarios siendo estos `member` y
`casual`. La compañía ha identificado que para obtener mayor rentabilidad es 
imprescindible aumentar el número de usuarios `member` para ello desean crear
una campaña de marketing dirigida a los usuarios `casual` para convencerlos de
cambiar su tipo de uso. Para que dicha campaña sea exitosa es necesario conocer
las características de uso que tiene cada tipo de usuario con el fin de dirigir
el marketing a puntos específicos que resulten interesantes para la audiencia.

Con la información obtenidos en la fase `analizar` podemos responder a la pregunta
fundamental

> ¿Qué diferencias hay entre un usuario `member` de uno `casual`?

A lo largo del documento escribiremos con letras cursivas las características
que diferencian a los dos tipos de usuarios.

## Análisis de los resultados

Nuestro conjunto de datos es un registro de los viajes en un **periodo de un año**.
No obstante, no tenemos forma de saber el número de usuarios que realizó esos
viajes, pues solo contamos con los registros de los viajes pero no hay un
identificador único para cada usuario que realizo un viaje. Con esta consideración
en mente, sabemos que de los `3470906` viajes que se realizaron entre **abril del
2020 y abril del 2021** el **62% de los viajes fueron realizados por usuarios `member`**
mientras que el restante **38% por usuarios `casual`**.

![Porcentajes por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_porcentaje_tipo_usuario.jpeg "Proporción de tipos de usuarios")

Esto nos dice que 
> Un usuario `casual` realiza muchos menos viajes a lo largo del
> periodo de un año que un usuario `member`. 

Para encontrar las razones que provocan este comportamiento analizaremos el 
comportamiento de otras variables contrastadas para cada tipo de usuario.

Si un usuario `member` realiza mucho más viajes en el año, es intuitivo llegar a
pensar que sus viajes son de mayor duración que los de los usuarios `casual`.

![Duración promedio de viaje por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_duracion_viaje_por_tipo.jpeg)

Tenemos una **diferencia promedio en la duración de los viajes de cada tipo de usuarios de 14 minutos**
siendo los usuarios `casual` los que tienen una mayor duración en sus viajes,
logrando un tiempo promedio de 29 minutos. 

> Los usuarios `casual` viajan menos veces pero por mayor tiempo que un usuario `member`.

Ahora, ¿qué sucede con las distancias que recorre cada tipo de usuario?. A 
continuación veamos la comparativa de esta variable.

![Distancia de viaje promedio por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_distancia_viaje_por_tipo.jpeg)


No observamos una diferencia sustancial, sabiendo que los cálculos son aproximados
y no consideran una trayectoria de viaje sino que es un cálculo en línea recta
podemos decir que son prácticamente idénticos, pues, una diferencia de 14 minutos
por 200 metros no es congruente y se puede atribuir a otro factor. La respuesta
a dicho factor no es posible responderla con certeza ya que nuestro conjunto de
datos no es explicito al respecto, pero siguiendo el camino que los datos nos
muestran podemos seguir con la siguiente variable que muestra el número de viajes
que realiza cada usuario a lo largo de la semana para así poder llegar a una
conclusión fundamentada del planteamiento anterior sobre las diferencias en las
duraciones de viaje a pesar de no existir una diferencia significativa en las
distancias que recorren.

La hipótesis que planteo para el cuestionamiento del párrafo anterior es que un 
usuario `casual` se caracteriza por tener viajes enfocados en la recreación. 
Por lo tanto, tendríamos que observar un aumento en su número de viajes a medida
que se acerca el fin de semana y una caída significativa durante los días 
laborales. Por otro lado, un usuario `member` usa el sistema de bicicletas 
para suplir una necesidad de transporte, posiblemente como medio de transporte
principal para dirigirse a sus espacios de trabajo. Por lo que veríamos un 
uso más constante en los días laborales y una disminución los fines de semana, 
sin descartar la posibilidad de que un usuario `member`, al ya tener su 
suscripción, también disfrute de un paseo dominical en bicicleta.

Para contrastar la hipótesis veamos lo que los datos muestran:

![Conteo de viajes diarios por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_conteo_viajes_diarios_por_tipo.jpeg "Número de pasajeros por día de la semana")

Con la visualización anterior comprobamos que la hipótesis se adapta a la naturaleza
de los datos y así obtenemos un nuevo comportamiento que diferencia a nuestros
usuarios.

> Los usuarios `casual` aumentan el número de viajes que realizan conforme se 
> acerca el fin de semana, teniendo un máximo los sábados; realizan un uso 
> casi constante de lunes a jueves siendo significativamente menor en
> comparación con los usuarios `member`. Por otro lado, los usuarios
> `member` realizan un uso más constante del servicio a lo largo de los días 
> laborales de la semana, aumentando igualmente sus viajes al acercarse el
> fin de semana, teniendo un mínimo los domingos.

Para seguir mejorando nuestra argumentación con respecto a la hipótesis, veamos
qué sucede con la duración promedio de los viajes a lo largo de una semana. 
Si la hipótesis es verdadera, tendríamos que ver que, al ser viajes con un 
propósito de transporte para un usuario `member`, estos viajes serán de menor
duración. Por el contrario, si la mayoría de los viajes que realiza un usuario
`casual` están enfocados en la recreación, refiriéndonos al descubrimiento o 
exploración de nuevos lugares en la ciudad o por disfrutar un paseo en 
bicicleta, los viajes deben ser de una duración mayor.

![Gráfico duración de viaje diaria por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_duracion_viajes_diarios_por_tipo.jpeg "Duración promedio de viajes diarios por tipo de usuario")

Estos resultados son lógicos, pues ya habíamos mostrado las diferencias en la
duración promedio de los viajes para cada tipo de usuario. Sin embargo, la
visualización anterior nos da un mayor entendimiento de cómo cada usuario
distribuye la duración de sus viajes a lo largo de la semana, destacando que la
duración presenta un comportamiento similar a lo que se observa en el número de
viajes que realizan. Para un usuario `member`, la duración de sus viajes se
observa muy similar para los días laborales, teniendo pequeñas diferencias los
fines de semana. Mientras que un usuario `casual` muestra un comportamiento más
variado, pero que igual coincide con la hipótesis de que este tipo de usuario
prefiere los viajes de fin de semana, pues no solo realiza más viajes estos
días, sino que también hace que sean más duraderos.

La última prueba que podemos agregar para afirmar nuestra hipótesis es observar
la distancia promedio que recorre cada tipo de usuario a lo largo de una semana.
Ya se había mencionado que no existían diferencias significativas en la 
distancia promedio que recorre cada usuario, sin embargo, si vemos el comportamiento
diario podemos obtener información útil.

![Distancia promedio de viajes diarios por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_distancia_promedio_viajes_diarios_por_tipo.jpeg "Distancia promedio de viajes diarios por tipo de usuario")


Observamos que para los usuarios `member` existe una menor dispersión de los datos
entre los días laborales, si hemos planteado la hipótesis de que su uso es para
transportarse encontraríamos justo lo que tenemos ahora, pues sus viajes no variarían
en cuánto a su origen y destino, ya que sus lugares de trabajo serían naturalmente,
constantes. Para corroborar esto numéricamente mostramos la salida que muestra
la desviación estándar del promedio de la distancia y duración de los viajes 
para ambos usuarios.


![Salida del cálculo de la dispersión de la distancia de viaje por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/salida_dispercion_distancia_de%20_viaje_por_dia_y_por_tipo_usuario.png "Salida del cálculo de la dispersión de la distancia de viaje por tipo de usuario")

Con esta última salida defendemos la hipótesis planteada pues la desviación estándar
para la distancia de los viajes realizados por los usuarios `member` es menor,
indicando que en efecto sus viajes son más uniformes durante los días laborales
que a la vez podemos atribuir a la función que le dan al sistema de bicicletas,
obteniendo las siguientes conclusiones:

> Un usuario `member` utiliza el sistema para suplir una necesidad de transporte
> a sus espacios laborales, mientras que un usuario `casual` tiene un enfoque
> más recreativo.

> Un usuario `member`realiza viajes con distancias similares a lo largo de la
> semana, por otro lado un usuario `casual` tiene mayor variación en sus
> recorridos.

Veamos a continuación la distribución en la cantidad de viajes que realiza cada
tipo de usuario a lo largo del año.

![Cantidad de viajes por tipo de usuario realizados por mes](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_duracion_viajes_mensuales_por_tipo.jpeg "Cantidad de viajes por tipo de usuario realizados por mes")

La información que podemos extraer de esta visualización es el comportamiento
estacional que tienen los usuarios. Se puede atribuir a las condiciones ambientales
que existen para cada estación del año. Ambos usuarios tienden a viajar más en las
épocas de verano teniendo una posible justificación en el periodo vacacional para 
los usuarios `casual` que, como justificamos anteriormente, al ser sus viajes con
fines recreativos pueden disfrutar de las condiciones climáticas más agradables de
esta época del año.

> Los meses de Junio, Julio y Agosto son los meses con mayor incidencia ciclista
> teniendo su máximo en Agosto.

Para finalizar nuestras observaciones de la información obtenida veamos algunas
particularidades en el tipo de bicicleta que prefieren los usuarios.

![Grafico de preferencias de bicicleta por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_preferencias_bicicletas.jpeg "Grafico preferencias de bicicleta por tipo de usuario")

Al observar este gráfico no se observa una preferencia diferenciada por el tipo
de bicicleta para los dos tipos de usuario. Es evidente que ambos prefieren las
bicicletas de tipo `docked`, seguido de las bicicletas `electric` y finalizando
con las de tipo `classic`. El conjunto de datos no muestra información sobre
las características de cada una pero es evidente que si deseamos mantener contentos
a ambos tipos de usuarios debemos tener una alta disponibilidad de bicicletas
`docked`

> Ambos tipos de usuarios prefieren las `docked bikes`