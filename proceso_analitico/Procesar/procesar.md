# Procesamiento: Conjunto de datos de Cyclistyc

Alfredo Aburto
2024-01-05

## Objetivo 

En esta fase del analísis realizaremos las operaciones necesarias para obtener
los siguientes resultados:

1. Datos organizados
2. Datos limpios y filtrados
3. Una documentación detallada de cada operación

## Introducción

Como se menciono en la fase de `Prepararación` nuestro conjunto de datos incluye
la información sobre los viajes historicos en formato .zip, siendo en total
trece ficheros. Los conjuntos de datos superan los **cien mil registros** es 
por eso que definir una herramienta adecuada para procesar los datos y 
posteriormente poder analizarlos de manera eficiente y profesional es de vital
importancia. Por lo anterior, en este documento incluimos la definición de la
herramienta a utilizar y la documentación de la limpieza de los datos para 
poder tener un flujo de trabajo adecuado y poder avanzar en el proceso de
analisís de los datos sin contratiempos.

## Importación de los datos

``` r
# Instala dplyr

# install.packages("tydyverse")

# Carga del paquete
library(tidyverse)
```

### Automatización de lectura de los archivos

La forma en la que se ha realizado la adquisición de los datos nos llevo a tener
archivos aislados con nombres diferentes, para poder realizar la lectura de los
archivos .csv extraidos y unirlos en un data frame creamos el siguiente script
que automatiza dicha tarea. A continuación documentamos su funcionamiento:

1. Inicializamos una lista que se utilizará para almacenar cada uno de los data
   frames importados
   
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
        -  Aquí, se utiliza [[ para acceder al elemento de la lista 
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