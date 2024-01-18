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

![Proporción de tipos usuarios](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_conteo_tipo_usuario.png "Proporción de tipos de usuarios")

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