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
  scale_y_continuous(limits = c(0,20))
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

Aquí obtuvimos un subconjunto de `cyclistic_data` para poder trabajar con él más eficientemente. Utilizamos `member_casual` y `distancia_viaje`, esta última con el objetivo de poder calcular la distancia de viaje promedio en metros y la primera para poder relacionar ambas variables.

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

Podemos observar que un usuario `member` recorre 2.4 km en promedio; por otro lado, un usuario `casual` recorre 2.6 km. Las diferencias son de 200 m, mientras que las diferencias de tiempo son aproximadamente 2.6 minutos.