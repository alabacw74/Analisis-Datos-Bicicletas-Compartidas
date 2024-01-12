# Procesamiento: Conjunto de datos de Cyclistyc

Alfredo Aburto
2024-01-05

# Índice

- [Introducción](#introducción)
- [Importación de los datos](#importación-de-los-datos)
   - [Automatización de lectura de los archivos](#automatización-de-lectura-de-los-archivos)
- [Limpieza de los datos](#limpieza-de-los-datos)
   - [Verificar tipos de datos](#verificar-tipos-de-datos)
      - [Creación de la Lista de Tipos de Datos](#paso-1-creación-de-la-lista-de-tipos-de-datos)
      - [Comparación de Tipos de datos](#paso-2-comparación-de-tipos-de-datos)
      - [Unificar tipos de datos](#paso-3-unificar-tipos-de-datos)
   - [Conversión de Columnas a Tipo Character en Data Frames](#conversión-de-columnas-a-tipo-character-en-data-frames)
   - [Unificar datos en un único data frame](#unificar-datos-en-un-único-data-frame)
   - [Separar started_at y ended_at](#separar-started_at-y-enden_at)
   - [Convertir fecha_inicio, fecha_finalizacion, hora_inicio, y hora_finalizacion](#convertimos-fecha_inicio-fecha_finalizacion-hora_inicio_-hora-finalizacion)
   - [Procesamiento de datos nulos](#procesamiento-de-datos-nulos)
      - [Verificar observaciones con datos nulos](#verificar-duplicados)
   - [Verificar duplicados](#verificar-duplicados)
   - [Ordenar los datos](#ordenar-los-datos)
   - [Almacenar conjunto de datos final en formato .rds](#almacenamos-nuestro-conjunto-de-datos-final)

## Objetivo 

En esta fase del analísis realizaremos las operaciones necesarias para obtener
los siguientes resultados:

1. Datos organizados
2. Datos limpios y filtrados
3. Una documentación detallada de cada operación

## Introducción

Como se mencionó en la fase de `Preparación`, nuestro conjunto de datos incluye la información sobre los viajes históricos en formato .zip, siendo en total trece ficheros. Los conjuntos de datos superan los **tres millones de registros**. Es por eso que definir una herramienta adecuada para procesar los datos y, posteriormente, poder analizarlos de manera eficiente y profesional es de vital importancia. Por lo anterior, en este documento incluimos la definición de la herramienta a utilizar y la documentación de la limpieza de los datos para poder tener un flujo de trabajo adecuado y poder avanzar en el proceso de análisis de los datos sin contratiempos.

## Importación de los datos

``` r
# Instala dplyr

# install.packages("tydyverse")

# Carga del paquete
library(tidyverse)
```

### Automatización de lectura de los archivos

**Corrección:**

La forma en que se ha realizado la adquisición de los datos nos llevó a tener 
archivos aislados con nombres diferentes. Para poder realizar la lectura de los
 archivos .csv extraídos y unirlos en un data frame, creamos el siguiente script
  que automatiza dicha tarea. A continuación, documentamos su funcionamiento:

1. Inicializamos una lista que se utilizará para almacenar cada uno de los data
   frames importados.
   
``` r
lista_dataframes <- list()
```

2. Creamos una estructura que realiza las siguientes acciones (ver paso
   correspondiente en el bloque de código):
   
    1. Creamos el ciclo for con una variable j que toma los valores de 20 y 21,
       esta variable se utiliza para crear dinámicamente los nombres de los
       archivos a leer en específico el año, interesándonos 2020 y 2021.
    2. Con la estructura if buscamos modificar el valor de la variable i del
       paso 3, ya que los ficheros tienen la estructura "202004, 202005" y
       asi sucesivamente hasta llegar a la terminación 12 y luego comenzando
       el conteo con "202101" hasta la terminación 04. Es por lo anterior que
       deseamos que la variable i tome valores diferentes según el año en el
       que estemos.
    3. Creamos el ciclo for con una variable i que, como se mencionó en el paso
       2 toma valores diferentes según el año en el que nos encontremos. Para
       el año 2020 tomará el conjunto [4, 12] y para el año 2021 [1, 4]. Esta
       variable se utiliza para crear dinámicamente en conjunto con la variable
       j el nombre del archivo que se debe leer.
    4. Genera el nombre de cada variable que almacenara los datos.
          - paste: concatena cadenas de texto para el nombre
          -sprintif: formatea la variable i para que se concatene como un
                     número de dos dígitos cuando es necesario, esto se
                     logra agregando un "0" para números del 1 al 9
    5. Creamos el nombre de los ficheros fuente que deseamos importar de forma
       dinámica, como se mencionó en el paso 2, su estructura varía siguiendo
       un año y un número según el año en el que estemos. Para ello fue necesario
       anidar una función paste dentro de otra función paste.
          - El primer paste genera el año dinámicamente (utiliza a j)
          - El segundo paste genera el número que debe ir junto al año (utiliza
            a i) y además concatena otra cadena de texto que forma parte del
            nombre de los ficheros fuente
    6. En esta sección, se utiliza read_csv para leer el archivo CSV 
    correspondiente y se asigna a una variable con el nombre generado en 
    la variable `nombre_variable`utilizando `assign`. Luego, ese data frame sé 
    agrega a la lista lista_dataframes utilizando la notación de doble 
    corchete [[ ]].
        -  Aquí, se utiliza `[[`` para acceder al elemento de la lista 
           lista_dataframes con el nombre almacenado en nombre_variable. 
           get(nombre_variable) devuelve el valor de la variable con el nombre 
           almacenado en nombre_variable. Luego, ese valor se asigna al elemento 
           correspondiente de lista_dataframes. En resumen, esta línea 
           agrega el data frame recién creado a la lista utilizando el 
           nombre almacenado en periodo como clave.
           
    El bloque de código subsecuente realiza lo descrito anteriormente pero con
    un valor de j igual a 21
``` r
# Paso 1
for (j in 20:21){
   # Paso 2
   if(j == 20){
     #  Paso 3
     for (i in 4:12) {
     
     # Paso 4
     nombre_variable <- paste("periodo_2020",
                              sprintf("%02d", i), sep="")
     # Paso 5
     archivo_csv <- paste("~/Desktop/Analisis_de_datos/Cyclistic_Datasets/",
                          paste("20", j, sprintf("%02d", i),
                                "-divvy-tripdata.csv",sep = ""),
                          sep=""
                          )
     # Paso 6
     # Lee el archivo CSV y asigna el resultado a la lista de data frames
     assign(nombre_variable, read_csv(archivo_csv))
     lista_dataframes[[nombre_variable]] <- get(nombre_variable)
     }
   }else{
     for (i in 1:4){
       # Genera el nombre del periodo y del archivo CSV
     nombre_variable <- paste("periodo_",
                              paste("20", j, sprintf("%02d", i), sep=""),
                              sep=""
                              )
     archivo_csv <- paste("~/Desktop/Analisis_de_datos/Cyclistic_Datasets/",
                          paste("20", j, sprintf("%02d", i),
                                "-divvy-tripdata.csv",sep = ""),
                          sep=""
                          )
   
     # Lee el archivo CSV y asigna el resultado a la lista de data frames
     assign(nombre_variable, read_csv(archivo_csv))
     lista_dataframes[[nombre_variable]] <- get(nombre_variable)
     }
       
   }
 }
```

## Limpieza de los datos

### Verificar tipos de datos

En esta sección del análisis, se busca verificar que todos los data frames 
tengan las mismas columnas con el mismo tipo de dato. Esto es esencial antes de
combinar los data frames en uno solo.

#### Paso 1: Creación de la Lista de Tipos de Datos

Para cada periodo de tiempo (ya sea del año 2020 o 2021), se crea una entrada 
en la lista `tipos_de_datos`. Se utiliza un bucle anidado para iterar sobre 
los periodos y las fechas, obteniendo así los tipos de datos de cada columna 
de cada data frame.

```r
tipos_de_datos <- list()

for (j in 20:21) {
  if (j == 20) {
    for (i in 4:12) {
      nombre_variable <- paste("periodo_2020", sprintf("%02d", i), sep="")
      tipos_de_datos[[nombre_variable]] <- sapply(get(nombre_variable), class)
    }
  } else {
    for (i in 1:4) {
      nombre_variable <- paste("periodo_", paste("20", j, sprintf("%02d", i), 
                                                 sep=""), sep="")
      tipos_de_datos[[nombre_variable]] <- sapply(get(nombre_variable), class)
    }
  }
}
```

#### Paso 2: Comparación de Tipos de datos

Después de obtener los tipos de datos de cada columna de cada data frame, se 
realiza una comparación para verificar si todas las columnas tienen el mismo 
tipo de dato en todos los data frames.

```r
comparacion_tipos <- all(sapply(tipos_de_datos[-1],
                                function(x) identical(x, 
                                                      tipos_de_datos[[1]])))
if (comparacion_tipos) {
  print("Todos los data frames tienen las mismas columnas con el 
        mismo tipo de dato.")
} else {
  print("Hay columnas con diferentes tipos de datos en los siguientes data frames:")
  for (i in 2:length(tipos_de_datos)) {
    
    if (!all(sapply(seq_along(tipos_de_datos[[1]]),
                    function(j) identical(tipos_de_datos[[i]][[j]], 
                                          tipos_de_datos[[1]][[j]])))) {
      columnas_diferentes <- 
        names(tipos_de_datos[[i]])[!(sapply(seq_along(tipos_de_datos[[1]]),
                function(j) identical(tipos_de_datos[[i]][[j]],
                                      tipos_de_datos[[1]][[j]])))]
      
      print(paste("En el data frame", names(tipos_de_datos)[i], "las columnas
                  siguientes tienen tipos de datos diferentes:", 
                  paste(columnas_diferentes, collapse = ", ")))
    }
  }
}
```

Este código proporciona una salida informativa sobre la consistencia de los 
tipos de datos en los data frames, señalando si todos son consistentes o, en 
caso contrario, identificando las columnas específicas y los data frames donde 
se encuentran diferencias.

#### Paso 3: Unificar tipos de datos

Con la sálida del código anterior podemos corregir las discrepancias en los
tipos de datos de algunas columnas:

Como se observa las columnas que presentan diferencias son
`start_station_id` y `end_station_id`. Para los data frame que nos proporciona
el código anterior son de tipo `character`. Considero que es mejor trabajar 
estos identificadores con el tipo `character` que con el tipo `double` que es
el tipo de dato con el que se encuentran en todos los demás data frame. Por lo
anterior, procedemos a convertir dichas columnas a tipo `character`

#### Conversión de Columnas a Tipo Character en Data Frames

El siguiente bloque de código tiene como objetivo convertir las columnas 
`start_station_id` y `end_station_id` de los data frames almacenados en una l
ista (`lista_dataframes`) a tipo character. Esto es útil cuando se desea 
trabajar con identificadores únicos que contienen solo números y se prefiere 
tratarlos como caracteres. A continuación, se presenta una explicación 
detallada del código:

1. **Definición de la función `convertir_a_character`:**
```R
   convertir_a_character <- function(df) {
     mutate(df, 
            start_station_id = as.character(start_station_id),
            end_station_id = as.character(end_station_id))
   }
```

Se crea una función llamada `convertir_a_character` que toma un data frame `df` 
como entrada. Utiliza la función `mutate` del paquete `dplyr` para convertir 
las columnas `start_station_id` y `end_station_id` a tipo character.

2. **Iteración sobre los valores de `j` y `i`:**
```R
   for (j in 20:21) {
     if (j == 20) {
       for (i in 4:11) {
         nombre_variable <- paste("periodo_2020", sprintf("%02d", i), sep="")
         lista_dataframes[[nombre_variable]] <- convertir_a_character(lista_dataframes[[nombre_variable]])
       }
     } else {
       for (i in 1:4) {
         nombre_variable <- paste("periodo_", paste("20", j, sprintf("%02d", i), sep=""), sep="")
         lista_dataframes[[nombre_variable]] <- convertir_a_character(lista_dataframes[[nombre_variable]])
       }
     }
   }
```
Se itera sobre los valores de `j` (20 y 21) para los años 2020 y 2021. 
Dependiendo de la condición `if`, se ajusta la variable `i` y se generan los 
nombres de las variables. Luego, se aplica la función `convertir_a_character`
a los data frames correspondientes almacenados en `lista_dataframes`.

3. **Observaciones:**
   - Asegúrate de tener instalado el paquete `dplyr` antes de ejecutar este 
     código.
   - La función `convertir_a_character` utiliza `mutate` para realizar la 
     conversión de tipo.

```{r}
# Combina todos los data frames en uno solo
conjunto_datos <- bind_rows(lista_dataframes)
View(conjunto_datos)
```

### Unificar datos en un único data frame

Para poder trabajar eficientemente con todo nuestro conjunto de datos, crearemos
un data frame preeliminar llamado `conjunto_datos`.

```r
# Combina todos los data frames en uno solo
conjunto_datos <- bind_rows(lista_dataframes)
View(conjunto_datos)
```
### Obtenemos la duracion del viaje

**Corrección:**

Es importante primero obtener la duración del viaje y después separar las 
variables de `fecha_inicio`, `hora_inicio`, `fecha_finalizacion` y 
`hora_finalizacion`. Invertir el orden provoca que sea más complicado sacar 
las diferencias de tiempo, ya que separarlas solo toma en cuenta las horas, 
pero pudieron existir viajes que ocurrieron en días diferentes, provocando que 
las diferencias de hora sean negativas. Realizamos posteriormente la separación 
de `started_at` y `ended_at`, y su conversión a un objeto de tipo fecha.

```r
cyclistic_data <- conjunto_datos %>% 
  mutate(
    duracion_viaje = as.numeric(difftime(ended_at, started_at, units = "mins"))
  ) %>% 
  separate(started_at, into = c("fecha_inicio", "hora_inicio"), sep = " ") %>%
  separate(ended_at, into = c("fecha_finalizacion", "hora_finalizacion"), sep = " ") %>% 
  filter(duracion_viaje > 0) %>%
  mutate(
    fecha_inicio = ymd(fecha_inicio),
    fecha_finalizacion = ymd(fecha_finalizacion)
  )
```

### Verificar consistencia de los datos

El proovedor de datos había informado lo siguiente:

> Los datos se han procesado para eliminar los viajes que realiza el personal 
mientras presta servicio e inspecciona el sistema; y cualquier viaje que dure 
menos de 60 segundos (posiblemente salidas en falso o usuarios que intenten 
volver a acoplar una bicicleta para asegurarse de que esté segura).

No obstante, se había procedido a la fase de analizar sin verificar que esta
información y al querer implementar un proceso de analisís ha salido a la luz
que existen observaciones con diferencias de latitud y longitud igual a cero.

Esto se interpreta como una salida en falso o viajes que realmente no se 
realizaron. Para poder eliminar esto seguimos el siguiente código:

```r 
# Cargar la biblioteca 'geosphere' para funciones de cálculo de distancias
#install.packages("geosphere")
library(geosphere)

```

Nuestro data_frame tiene las columnas `start_lat`, `start_lng`, `end_lat`, 
`end_lng`. Estas columnas contienen coordenadas de inicio y fin de cada viaje

Calcularemos la distancia `haversine` para cada viaje y almacenaremos el 
resultado en una nueva columna 'distancia_viaje'

```r
cyclistic_data$distancia_viaje <- distHaversine(cbind(cyclistic_data$start_lng,
                                                cyclistic_data$start_lat), 
                                          cbind(cyclistic_data$end_lng, 
                                                cyclistic_data$end_lat))

# La columna 'distancia_viaje' ahora contiene la distancia haversine en metros 
# para cada viaje
```

Contamos el número de observaciones que tienen una distancia de viaje de cero

```r
cyclistic_data %>% 
  filter(duracion_viaje == 0) %>% 
  summarise(cantidad = n())
```

Eliminamos estas observaciones y las almacenamos en el conjunto de datos

```r
cyclistic_data <- cyclistic_data %>% 
  filter(distancia_viaje != 0)
```
### Verificamos consistencia de los datos

Durante la fase de analisís se hizo notar la existencia de viajes de bicicleta
con una duración mayor a un día, vamos a revisar estos viajes:

```{r}
cyclistic_data %>% 
  filter(duracion_viaje > 1440 & distancia_viaje > 10000) %>% 
  arrange(duracion_viaje)
```
La salida anterior muestra que existen viajes de más de 10 km con una duración
de más de un día (un día tiene 1440 minutos). Esto no resulta de un uso 
cotidiano, pues a una velocidad promedio de 10 km/h se recorrerían estas 
distancias en mucho menos tiempo. Sugiere que existe un grupo de usuarios que 
solicita una bicicleta y, en su recorrido, decide detenerse por alguna
actividad y luego devuelve la bicicleta, añadiendo tiempo a su recorrido. 
En nuestro caso, dicha actividad se extiende más de 24 horas, por lo que 
podemos decir que, para fines de nuestro análisis, dicho grupo de usuarios
debe ser analizado por separado.

Por otro lado, un viaje de 24 horas tampoco resultaría de un uso cotidiano. 
Se reducirá el área de análisis a viajes con una duración de menos de 600
minutos, es decir, diez horas. A continuación, se sustenta esta decisión:

```{r}
cyclistic_data %>% 
  filter(duracion_viaje > 600) %>% 
  arrange(-distancia_viaje)
```


```{r}
cyclistic_data <- cyclistic_data %>% 
  filter(duracion_viaje < 600)
```

### Procesamiento de datos nulos

Para poder ver el conjunto de observaciones que cuentan con datos nulos
ejecutamos el siguiente código:

``` r
filas_con_nulos <- tu_data_frame[apply(is.na(tu_data_frame), 1, any), ]
```

1. `is.na(tu_data_frame)`: Esta expresión crea una matriz booleana del mismo
tamaño que `tu_data_frame`, donde cada elemento es `TRUE` si el valor 
correspondiente en `tu_data_frame` es `NA` (nulo), y `FALSE` en caso contrario.

2. `apply(is.na(tu_data_frame), 1, any)`: La función `apply` se utiliza para 
aplicar una función a los márgenes de una matriz o array. En este 
caso, aplicamos la función `any` a lo largo de las filas (especificado por
el argumento `1`). La función `any` devuelve `TRUE` si al menos uno de los 
valores es `TRUE` en cada fila. Esto nos da un vector lógico que indica qué
filas tienen al menos un valor nulo.

3. `tu_data_frame[apply(is.na(tu_data_frame), 1, any), ]`: Finalmente, 
utilizamos este vector lógico para seleccionar las filas de `tu_data_frame` 
donde al menos un valor es nulo. Entonces, `filas_con_nulos` contendrá 
únicamente las filas que tienen datos nulos.

```r
str(filas_con_nulos)
```

Tenemos `234 800` columnas que contienen datos nulos, vamos a verificar 
el número de nulos en cada una de las columnas

```r
# Nombre del data frame
nombre_data_frame <- filas_con_nulos

# Inicializar una lista para almacenar los conteos de nulos por columna
conteos_nulos <- list()

# Iterar sobre todas las columnas del data frame
for (nombre_columna in colnames(nombre_data_frame)) {
  # Contar valores nulos en la columna específica
  nulos_en_columna <- sum(is.na(nombre_data_frame[[nombre_columna]]))
  
  # Imprimir el resultado
  print(paste("Número de valores nulos en", nombre_columna, ":", nulos_en_columna))
  
  # Almacenar el resultado en la lista
  conteos_nulos[[nombre_columna]] <- nulos_en_columna
}

```

La salida nos muestra que la mayor cantidad de nulos la encontramos en las
variables `start_station_name`, start_station_id, end_station_name y 
end_station_id. Es dceri, tenemos la mayor densidad de nulos en información
referente a las estaciones de inicio y de termino del viaje. Para fines de
nuestro analisis es importante conocer esta información ya que se busca poder
determinar con ella una distancia aproximada de viaje recorrido. Sin embargo,
estos datos representan un 6.14 % del universo de datos. Por lo que omitiremos
estos datos para fines de conocer la distancia aproximada de viaje pero pueden
proporcionar información útil sobre el tipo de miembro.

### Verificar duplicados

```r
# Nombre de tu data frame
nombre_data_frame <- conjunto_datos

# Verificar si hay duplicados en el data frame
hay_duplicados <- any(duplicated(nombre_data_frame))

# Imprimir el resultado
if (hay_duplicados) {
  print("Existen datos duplicados en el data frame.")
} else {
  print("No hay datos duplicados en el data frame.")
}
``````
La salida indica que no existen datos duplicados, para terminar nuestro
procesamiento procedemos a ordenarlos

### Ordenar los datos

Como último paso, ordenamos nuestros datos del mas reciente al mas antiguo,
terminando con esto nuestro procesamiento de datos

```r
cyclistic_data <- cyclistic_data %>% 
  arrange(desc(fecha_inicio))
```

### Almacenamos nuestro conjunto de datos final

El formato de archivo `.rds` es un formato de serialización en R que se utiliza
para guardar objetos de R en un archivo binario. "RDS" significa "R Data 
Serialization". Es un formato eficiente para almacenar objetos R, y se utiliza 
comúnmente para guardar datos, modelos, marcos de datos y otros objetos de R 
para su posterior uso.

Algunas características y consideraciones sobre el formato `.rds`:

1. **Eficiencia de Almacenamiento:** El formato `.rds` es binario, lo que
significa que es más eficiente en términos de espacio en comparación con otros
formatos de almacenamiento de datos, como CSV o XLSX.

2. **Soporte para Diversos Objetos:** Puedes guardar una variedad de objetos de
R en formato `.rds`, incluyendo marcos de datos, listas, modelos, entre otros.

3. **Preservación de Metadatos:** A diferencia de algunos formatos de 
intercambio de datos, el formato `.rds` preserva los atributos y metadatos 
asociados con los objetos de R, lo que es útil para la recuperación precisa
de los datos.

4. **Función de Lectura y Escritura en R:** Puedes utilizar las funciones 
`saveRDS()` para guardar un objeto en formato `.rds` y `readRDS()` para 
leer el objeto de vuelta a R.

El formato `.rds` es particularmente útil cuando deseas compartir datos o 
resultados de análisis con otros usuarios de R o cuando necesitas almacenar 
objetos de manera eficiente para un uso futuro.

```r
saveRDS(cyclistic_data,
        "~/Desktop/Analisis_de_datos/Cyclistic_Datasets/cyclistic_data.rds")
```

```
