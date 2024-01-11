# Analisís: Conjunto de datos de Cyclistic

Alfredo Aburto
2024-01-10

## Objetivo
En esta fase del analisís de datos se busca obtener la información valiosa que
nuestro conjunto de datos pueda ofrecer. En este punto contamos con datos
preparados y procesados, listos para ser analizados. Es por ello que en esta
sección utilizaremos herramientas estadísticas que nos permitan conocer el 
comportamiento diferenciado entre los miembros casuales y suscriptores anuales
del sistema de prestamo de bicicletas.

En esta fase no presentaremos una discusión sobre los resultados obtenidos, 
solo se mencionarán algunas premisas y se presentaran los resultados. En
la siguiente fase se realizara una discusión y exposición de lo encontrado.

## Cargar entorno de trabajo

```r
library(tidyverse)
```

## Importar nuestro conjunto de datos

```r
cyclistic_data <- readRDS("~/Desktop/Analisis_de_datos/Cyclistic_Datasets/cyclistic_data.rds")
```

<!-- Agregar imagenes tibble_conjunto_datos_0* -->

Como primer punto de analisís nos enfocaremos en encontrar las proporciones
de usuarios de tipo `member` para los que cuentan con una suscripción anual y de
tipo `casual` para aquellos que no la tienen pero que hacen uso del sistema con
pagos por día o por viaje.

```r
conteo_tipo_usuario <- cyclistic_data %>%
  filter(!is.na(member_casual)) %>%
  group_by(member_casual) %>%
  summarise(count = n())
```

<!--Agregar imagen tibble_conteo_tipo_usuario-->

### Visualización

Para mostrar lo obtenido en `conteo_tipo_usuario` creamos un gráfico de pastel
que muestra los porcentajes que ocupan cada tipo de usuario en nuestro conjunto
de datos utilizando `ggplot2` contenido en la libreria `tidyverse`.

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

En este gráfico observamos que nuestro conjunto de datos tiene un 62% de 
usuarios con membresia anual y un 38% con uso casual. Recordemos que nuestra
labor empresarial es identificar las diferencias que existen en el uso del
sistema de movilidad a tráves de los datos, lo cuál realizamos en las siguientes
secciones.

## Duración de viaje

Una de las premisas que se tienen sobre las diferencias entre los tipos de
usuarios es que se caracterizan por tener tiempos de viaje diferentes. Se
esperaría que los suscriptores anuales tengan un tiempo promedio de viaje
mayor al de los miembros casuales.

Para comprobar nuestra hipotesis creamos la variable `duracion_viaje_por_tipo`
que es un subconjunto de los datos generales `cyclistic_data`. Este nuevo 
subconjunto utiliza las variable `member_casual` y `duracion_viaje` (esta 
ultima fue generada en la fase anterior del analisís `Procesar`) para 
posteriormente agrupar el conjunto de datos por tipo de usuario y calcular
para cada uno el tiempo promedio de duración de sus viajes.

```r
duracion_viaje_por_tipo <- cyclistic_data %>% 
  select(member_casual, duracion_viaje) %>% 
  group_by(member_casual) %>% 
  drop_na() %>% 
  summarise(tiempo_promedio_viaje = mean(duracion_viaje))
```
<!-- Agregar imagen tibble_tiempo_promedio_viaje_por_tipo_usuario.png -->

### Visualización

Nuestra hípotesis fue refutada y para hacer evidente esto, creamos un gráfico
de barras que compara la variable `tiempo_promedio_viaje`contrastada por
tipo de usuario.

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

Para crear esta visualización usamos la geometría `geom_col` para poder hacer
uso de dos variables. Corregimos los colores para que fueran consistentes con
la paleta de colores utilizada en el gráfico anterior. Al final agregamos 
etiquetas para especificar los nombres de los ejes y establecemos los limites de
los ejes. Este código se útiliza para la mayoria de visualizaciones teniendo
cada una cierta particularidad.

Nuestro gráfico muestra que los viajes de los usuarios `casual` tienen una mayor
duración promedio que los usuarios de tipo `member`, sería interesante poder ver
si existe una relación entre la duración de los viajes realizados con la 
distancia recorrida en cada una según el tipo de usuario, la intuición diría que
si pero los datos serán nuestro sustento.