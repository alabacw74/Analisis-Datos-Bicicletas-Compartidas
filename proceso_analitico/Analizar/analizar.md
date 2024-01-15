# Analisís: Conjunto de datos de Cyclistic

Alfredo Aburto
2024-01-10

## Objetivo
En esta fase del análisis de datos, buscamos obtener la información valiosa que nuestro conjunto de datos pueda ofrecer. En este punto, contamos con datos preparados y procesados, listos para ser analizados. Por ello, en esta sección utilizaremos herramientas estadísticas que nos permitan conocer el comportamiento diferenciado entre los miembros casuales y los suscriptores anuales del sistema de préstamo de bicicletas.

En esta fase, no presentaremos una discusión sobre los resultados obtenidos; solo se mencionarán algunas premisas y se presentarán los resultados. En la siguiente fase, se realizará una discusión y exposición de lo encontrado.

## Cargar entorno de trabajo

```r
library(tidyverse)
```

## Importar nuestro conjunto de datos

```r
cyclistic_data <- readRDS("~/Desktop/Analisis_de_datos/Cyclistic_Datasets/cyclistic_data.rds")
```

![Conjunto de datos de Cyclistic (Procesados)](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_conjunto_datos_01.png)

![Conjunto de datos de Cyclistic (Procesados)](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_conjunto_datos_02.png)

Como primer punto de análisis, nos enfocaremos en encontrar las proporciones de usuarios de tipo `member`, para aquellos que cuentan con una suscripción anual, y de tipo `casual`, para aquellos que no la tienen pero que hacen uso del sistema con pagos por día o por viaje.

```r
conteo_tipo_usuario <- cyclistic_data %>%
  filter(!is.na(member_casual)) %>%
  group_by(member_casual) %>%
  summarise(count = n())
```

![Conteo de usuarios por tipo de suscripción](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_conteo_tipo_usuario.png)

### Visualización

Para mostrar lo obtenido en `conteo_tipo_usuario` creamos un gráfico de pastel
que muestra los porcentajes que ocupan cada tipo de usuario en nuestro conjunto
de datos utilizando `ggplot2` contenido en la biblioteca `tidyverse`.

```r
# Crear un gráfico circular con ggplot2
grafico_porcentaje_tipo_usuario <- ggplot(conteo_tipo_usuario, aes(x = "", y = count, fill = member_casual)) +
  geom_bar(stat = "identity", width = 1, color = "white") +
  coord_polar("y", start = 0) +
  ggtitle("Porcentaje de cada tipo de usuario") +
  labs(caption="alabacw74 / Datos de divybykes") +
  theme_void() +
  theme(legend.position = "none") +
  geom_text(aes(label = paste0(member_casual, ": ", scales::percent(count/sum(count)))), position = position_stack(vjust = 0.5))

grafico_porcentaje_tipo_usuario

```

![Porcentajes por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_porcentaje_tipo_usuario.jpeg)

En este gráfico, observamos que nuestro conjunto de datos tiene un 62% de usuarios con membresía anual y un 38% con uso casual. Recordemos que nuestra labor empresarial es identificar las diferencias que existen en el uso del sistema de movilidad a través de los datos, lo cual realizamos en las siguientes secciones.

## Duración de viaje

Una de las premisas que se tienen sobre las diferencias entre los tipos de usuarios es que se caracterizan por tener tiempos de viaje diferentes. Se esperaría que los suscriptores anuales tengan un tiempo promedio de viaje mayor al de los miembros casuales.

Para comprobar nuestra hipótesis, creamos la variable `duracion_viaje_por_tipo`, que es un subconjunto de los datos generales `cyclistic_data`. Este nuevo subconjunto utiliza las variables `member_casual` y `duracion_viaje` (esta última fue generada en la fase anterior del análisis `Procesar`) para posteriormente agrupar el conjunto de datos por tipo de usuario y calcular, para cada uno, el tiempo promedio de duración de sus viajes.

```r
duracion_viaje_por_tipo <- cyclistic_data %>% 
  select(member_casual, duracion_viaje) %>% 
  group_by(member_casual) %>% 
  drop_na() %>% 
  summarise(tiempo_promedio_viaje = mean(duracion_viaje))
```
<!-- Agregar imagen tibble_tiempo_promedio_viaje_por_tipo_usuario.png -->
![Duración promedio de viaje por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_tiempo_promedio_viaje_por_tipo_usuario.png)

### Visualización

Nuestra hipótesis fue refutada y, para hacer evidente esto, creamos un gráfico de barras que compara la variable `tiempo_promedio_viaje` contrastada por tipo de usuario.

```r
grafico_duracion_viaje_por_tipo <- ggplot(data = duracion_viaje_por_tipo) +
  geom_col(mapping = aes(x=member_casual, y=tiempo_promedio_viaje,
                         fill=member_casual)) +
  scale_fill_manual(values = c("casual" = "#FF9999", "member" = "#66B2FF")) +  
  labs(title = "Duración de viaje promedio por tipo de usuario",
       x = "Tipo de usuario",
       y = "Duración promedio de viaje (min)",
       fill = "Tipo de usuario",
       caption = "alabacw74 / Datos de divybykes") +
  scale_y_continuous(limits = c(0,30)) +
  annotate("text",
           x=1, y=2,
           label= "29.04 min",
           color="black",
           fontface="bold",size=4.0) +
  annotate("text",
           x=2, y=2,
           label= "14.91 min",
           color="black",
           fontface="bold",size=4.0)

grafico_duracion_viaje_por_tipo
```

![Duración promedio de viaje por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_duracion_viaje_por_tipo.jpeg)

Para crear esta visualización, usamos la geometría `geom_col` para poder hacer uso de dos variables. Corregimos los colores para que fueran consistentes con la paleta de colores utilizada en el gráfico anterior. Al final, agregamos etiquetas para especificar los nombres de los ejes y establecemos los límites de los ejes. Este código se utiliza para la mayoría de las visualizaciones, teniendo cada una cierta particularidad.

Nuestro gráfico muestra que los viajes de los usuarios `casual` tienen una mayor duración promedio que los usuarios de tipo `member`. Sería interesante poder ver si existe una relación entre la duración de los viajes realizados y la distancia recorrida en cada uno según el tipo de usuario. La intuición diría que sí, pero los datos serán nuestro sustento.

## Distancia de viaje

Antes de ver cómo se relaciona la duración con la distancia del viaje, veremos por separado cada una. En esta sección, utilizamos la distancia de viaje que se obtuvo durante el procesamiento de los datos con una librería muy interesante que de manera aproximada obtuvo la distancia del viaje utilizando la latitud y longitud iniciales. Ahora es tiempo de utilizar esa información para poder ver qué distancia promedio recorre cada tipo de usuario cada vez que viaja.
```r
distancia_viaje_por_tipo <- cyclistic_data %>% 
  select(member_casual, distancia_viaje) %>% 
  group_by(member_casual) %>% 
  summarise(promedio_distancia = mean(distancia_viaje))
```
<!-- Agregar imagen tibble_distancia_viaje_tipo_usuario.png-->
![Distancia de viaje por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_distancia_viaje_tipo_usuario.png)

Aquí obtuvimos un subconjunto de `cyclistic_data` para poder trabajar con él más eficientemente. Utilizamos `member_casual` y `distancia_viaje`, esta última con el objetivo de poder calcular la distancia de viaje promedio en metros y la primera para poder relacionar ambas variables. Nuestro conjunto de datos no contiene viajes con una
distancia inferior a cien metros, pues se consideró que no corresponden a un uso
cotidiano del sistema de bicicletas.

### Visualización 

Esta visualización muestra la duración promedio de viaje en `km`. Para ello, realizamos una operación de división por `1000` sobre la variable `promedio_distancia` que se obtiene en la generación de `distancia_viaje_por_tipo`. Aplicamos colores manualmente para mantener la nomenclatura para cada tipo de usuario y, en este caso, modificamos los saltos de valor para el eje y. Por último, agregamos anotaciones para mostrar de mejor manera los valores promedio.

```r
grafico_distancia_viaje_por_tipo <- ggplot(data = distancia_viaje_por_tipo) +
  geom_col(mapping = aes(x=member_casual, y=promedio_distancia/1000,
                         fill=member_casual)) +
  scale_fill_manual(values = c("casual" = "#FF9999", "member" = "#66B2FF")) +  
  labs(title = "Distancia de viaje promedio por tipo de usuario",
       x = "Tipo de usuario",
       y = "Distancia promedio de viaje (km)",
       fill = "Tipo de usuario",
       caption = "alabacw74 / Datos de divybykes") +
  scale_y_continuous(limits = c(0, 3),
                     breaks = seq(0, 3, by = 0.5)) +
  annotate("text",
           x=1, y=2,
           label= "2.6 km",
           color="black",
           fontface="bold",size=4.5) +
  annotate("text",
           x=2, y=2,
           label= "2.4 km",
           color="black",
           fontface="bold",size=4.5)

grafico_distancia_viaje_por_tipo
```

![Distancia de viaje promedio por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_distancia_viaje_por_tipo.jpeg)

Podemos observar que un usuario `member` recorre 2.37 km en promedio; por otro lado, un usuario `casual` recorre 2.55 km. Las diferencias son de aproximadamente 200 m, mientras que las diferencias de tiempo son aproximadamente 14.13 minutos.

## Distancia contra tiempo por tipo de usuario

Hasta aquí hemos visto que hay una diferencia muy grande entre las duraciones de
viaje pero no hay una diferencia muy significativa en las distancias (aproximadas)
que los usuarios recorren. El siguiente gráfico muestra la relación entre
la distancia de viaje en Km contra la duración del viaje en minutos:

```r
grafico_distancia_viaje_vs_duracion_suavizado_gam <- ggplot(data=cyclistic_data) +
  geom_point(mapping = aes(x = duracion_viaje/60, y=distancia_viaje/1000,
                           color=member_casual)) +
  facet_wrap(~member_casual) +
  geom_smooth(mapping = aes(x=duracion_viaje/60, y=distancia_viaje/1000),
              method = "gam",formula = y ~s(x)) +
  labs(title = "Viajes en bicicleta: Distancia de viaje vs Duración",
       subtitle = "Muestras para dos tipos de usuario con Suavizado GAM",
       caption = "alabacw74 / Datos de Divybykes",
       x = "Duración (horas)",
       y = "Distancia (km)")
```


El objetivo del código es generar un gráfico de dispersión que representa la 
relación entre la duración de los viajes en bicicleta (en horas) y la distancia 
recorrida durante esos viajes (en kilómetros). Además, se aplica un suavizado 
utilizando el modelo GAM (Generalized Additive Model) para visualizar tendencias
 generales en los datos.

A continuación, se presenta una descripción detallada del código:

1. `ggplot(data=cyclistic_data)`: Se crea un objeto ggplot utilizando los datos
 del marco de datos `cyclistic_data`.

2. `geom_point(mapping = aes(x = duracion_viaje/60, y=distancia_viaje/1000, color=member_casual))`: Se agregan puntos al gráfico de dispersión, mapeando la duración del viaje en el eje x (convertida a horas) y la distancia del viaje en el eje y (convertida a kilómetros). Además, se utiliza el color para distinguir entre los tipos de usuarios (`member_casual`).

3. `facet_wrap(~member_casual)`: Se dividen los gráficos en facetas según el tipo de usuario, creando paneles separados para cada categoría.

4. `geom_smooth(mapping = aes(x=duracion_viaje/60, y=distancia_viaje/1000), method = "gam", formula = y ~ s(x))`: Se agrega una línea de suavizado utilizando el modelo GAM para visualizar tendencias generales en los datos. La duración y la distancia del viaje se ajustan mediante el modelo GAM.

5. `labs(...)`: Se configuran los títulos y etiquetas del gráfico, incluyendo el título principal, el subtítulo, la leyenda y los ejes x e y.

### Visualizacion

![Distancia de viaje vs duracion](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_distancia_viaje_vs_duracion_suavizado_gam.jpeg)

La relevancia del gráfico radica en que nos permite visualizar patrones en los 
viajes que realiza cada tipo de usuario. Para los usuarios `casual`, se observan
viajes con distancias y duraciones más constantes; sus viajes suelen tener 
distancias similares, pero con duraciones cada vez más grandes. Por otro lado,
los usuarios `member` muestran un comportamiento más diferenciado. Sus viajes 
suelen ser de menor duración, teniendo un pico de duración entre la media hora
y la hora con una distancia de poco más de 5 km. Después de estos puntos, 
muestran un decaimiento constante, indicando que estos usuarios recorren 
menos distancias a medida que aumenta el tiempo de uso.

Para poder sustentar si realmente existe una tendencia entre la distancia y 
duración de los viajes, obtendremos la correlación en la siguiente sección.

## Correlación entre la distancia y la duración de viaje

La correlación nos permite ver si nuestros dos ejes de datos se comportan de la 
misma manera respecto a sus medias. Si esto sucede, se tendrá una correlación 
fuerte (un valor cercano a +1); por el contrario, se puede tener una correlación
baja o nula. Veamos cuál es su valor para nuestras variables para cada tipo 
de usuario.

```r
cyclistic_data %>% 
  group_by(member_casual) %>% 
  summarise(cor(duracion_viaje, distancia_viaje))
```

![Correlación entre duración y distancia de viaje](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/correlacion_distancia_duracion_viaje.png)

Estos resultados nos indican que no hay una correlación fuerte entre la duracion
y la distancia de viaje pero nos permite confirmar el comportamiento que habiamos
definido en el gŕafico anterior. Los `member` tienden a aumentar más su duración
de viaje cuando sus recorridos son más largos mientras que los casuales no
muestran este patrón, sino que mantienen una proporción similar entre la duración
de sus viajes y sus distancias.

## Número de pasajeros por día de la semana

El siguiente punto de enfoque en nuestro analisís es el número de pasajeros por
día de la semana. Para poder realizar este analisís es necesario extraer esta
información de nuestro conjunto de datos, el siguiente bloque de código lo logra:

```r
# Obtenemos el conteo de viajes por día de la semana
num_viaje_por_dia_tipo <- cyclistic_data %>%
  group_by(member_casual, viajes_dia_semana = weekdays(fecha_inicio)) %>%
  summarise(count = n())
```

```r
# Ordenamos los días de la semana para una mejor visualización
num_viaje_por_dia_tipo$viajes_dia_semana <-
  factor(num_viaje_por_dia_tipo$viajes_dia_semana,
         levels = c("lunes",
                    "martes",
                    "miércoles",
                    "jueves",
                    "viernes",
                    "sábado",
                    "domingo"), ordered = TRUE)

num_viaje_por_dia_tipo <- num_viaje_por_dia_tipo[order(num_viaje_por_dia_tipo$viajes_dia_semana),]

num_viaje_por_dia_tipo
```

![Numero de viajes por dia de la semana](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_conteo_viajes_por_dia_semana_por_tipo_usuario.png)

### Visualización

El siguiente gráfico muestra la distribución de viajes semanales realizados por cada tipo de usuario. Se observa que los usuarios `member` son los que realizan un mayor número de viajes a lo largo de la semana, manteniéndose casi constante. Por otro lado, los usuarios `casual` realizan un número de viajes significativamente bajo durante los días correspondientes entre el lunes y viernes, mostrando un aumento drástico el sábado y uno menos pronunciado los domingos.

```r
grafico_conteo_viajes_diarios_por_tipo <- ggplot(data = num_viaje_por_dia_tipo) +
  geom_col(mapping = aes(x = viajes_dia_semana, y = count, fill = viajes_dia_semana)) +
  facet_wrap(~member_casual) +
  labs(title = "Conteo de viajes diarios",
       subtitle = "Por tipo de usuario",
       x = "Día de la semana",
       y = "Número de viajes (miles)",
       fill = "Día de la semana",
       caption = "alabacw74 / Datos de divybykes") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1)) +
  scale_y_continuous(labels = scales::number_format(scale = 1e-3))

grafico_conteo_viajes_diarios_por_tipo
```

![Conteo de viajes diarios por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_conteo_viajes_diarios_por_tipo.jpeg)

## Duración de viaje promedio diario por tipo de usuario

Para poder comprender otra distinción entre los tipos de usuarios, nos enfocamos
en realizar visualizaciones que nos permitan obtener información sobre el 
comportamiento que les da identidad a cada uno. Para ello, las visualizaciones 
buscan extraer la información utilizando periodos de tiempo para observar cómo 
varían diferentes variables. En este caso, revisaremos la duración de los viajes 
que realiza cada usuario por día de la semana.

1. Creamos `tiempo_viaje_por_dia_tipo` para obtener un subconjunto de datos mas
manejable

```r 
tiempo_viaje_por_dia_tipo <- cyclistic_data %>% 
  group_by(member_casual, viajes_dia_semana = weekdays(fecha_inicio)) %>% 
  summarise(duracion_promedio = mean(duracion_viaje))
```

2. Ordenamos los dias de la semana para mejorar la visualización

```r
tiempo_viaje_por_dia_tipo$viajes_dia_semana <-
  factor(tiempo_viaje_por_dia_tipo$viajes_dia_semana,
         levels = c("lunes",
                    "martes",
                    "miércoles",
                    "jueves",
                    "viernes",
                    "sábado",
                    "domingo"), ordered = TRUE)

tiempo_viaje_por_dia_tipo <- tiempo_viaje_por_dia_tipo[order(tiempo_viaje_por_dia_tipo$viajes_dia_semana),]

tiempo_viaje_por_dia_tipo
```

![Salida de la duracion de viaje por dia de la semana](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_duracion_promedio_viajes_por_dia_semana_por_tipo_usuario.png)

## Visualización

El siguiente código genera la visualización pertinente:

```r
grafico_duracion_viaje_por_tipo <- ggplot(data = tiempo_viaje_por_dia_tipo) +
  geom_col(mapping = aes(x = viajes_dia_semana, y = duracion_promedio, fill = viajes_dia_semana)) +
  facet_wrap(~member_casual) +
  labs(title = "Duración promedio de viajes diarios",
       subtitle = "Por tipo de usuario",
       x = "Día de la semana",
       y = "Duración del viaje (min)",
       fill = "Día de la semana",
       caption = "alabacw74 / Datos de divybykes") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

grafico_duracion_viaje_por_tipo
```

Es evidente que los viajes de los usuarios `casual` son de mayor duración 
promedio, manteniéndose casi constante a lo largo de la semana y aumentando los 
fines de semana. Por otro lado, los viajes de los usuarios `member` también se
 mantienen casi constantes a lo largo de la semana, pero con una duración menor.

<!-- Analisis de dispersion usando la desviación estandar para saber que tan constantes se mantienen-->

![Gráfico duración de viaje diaria por tipo de usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/Visualizaciones/Grafico_duracion_viajes_diarios_por_tipo.jpeg "Duración promedio de viajes diarios por tipo de usuario")


## Distancia de viaje promedio diario por tipo de usuario

Continuando con el enfoque mencionado en la sección anterior, continuaremos el 
analisís mostrando el comportamiento de cada tipo de usuario en la distancia
promedio de sus viajes a lo largo de la semana. Igualmente que en la sección
anterior comenzaremos creando un subconjunto que nos permita seleccionar las
variables necesarias y a la vez poder ser más eficientes en su uso. El siguiente
bloque de código se encarga de ello:

```r
distancia_viaje_por_dia_tipo <- cyclistic_data %>% 
  group_by(member_casual, viajes_dia_semana = weekdays(fecha_inicio)) %>% 
  summarise(distancia_promedio = mean(distancia_viaje)) %>% 
  drop_na()
```
```r
distancia_viaje_por_dia_tipo$viajes_dia_semana <-
  factor(distancia_viaje_por_dia_tipo$viajes_dia_semana,
         levels = c("lunes",
                    "martes",
                    "miércoles",
                    "jueves",
                    "viernes",
                    "sábado",
                    "domingo"), ordered = TRUE)

distancia_viaje_por_dia_tipo <- distancia_viaje_por_dia_tipo[order(distancia_viaje_por_dia_tipo$viajes_dia_semana),]

distancia_viaje_por_dia_tipo
```

![tibble_distancia_promedio_viajes_por_dia_semana_por_tipo_usuario](https://github.com/alabacw74/analisis-datos-bicicletas-compartidas/blob/main/proceso_analitico/Analizar/images/tibble_distancia_promedio_viajes_por_dia_semana_por_tipo_usuario.png "Distancia promedio de viajes diarios por tipo de usuario")