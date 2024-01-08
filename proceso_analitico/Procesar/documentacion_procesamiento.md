# Documentación de Procesamiento: Conjunto de datos de Cyclistic

## Presentación

En este documento mostramos una documentación detallada de la funcionalidad
de cada bloque de código utilizado para automatizar tareas durante el
procesamiento del *Conjunto de datos de Cyclistic*

## Automatización de lectura de los archivos

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
        -  Aquí, se utiliza `[[` para acceder al elemento de la lista 
           lista_dataframes con el nombre almacenado en nombre_variable. 
           get(nombre_variable) devuelve el valor de la variable con el nombre 
           almacenado en nombre_variable. Luego, ese valor se asigna al elemento 
           correspondiente de lista_dataframes. En resumen, esta línea 
           agrega el data frame recién creado a la lista utilizando el 
           nombre almacenado en periodo como clave.
           
    El bloque de código subsecuente realiza lo descrito anteriormente pero con
    un valor de j igual a 21
    
```r
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

#### Paso 1: Inicializar lista

Aquí se inicializa una lista vacía llamada tipos_de_datos. Esta lista se 
utilizará para almacenar información sobre los tipos de datos de las variables 
en diferentes data frames.


```r
tipos_de_datos <- list()
```

#### Paso 2: Extraer y asignar el tipo de dato de cada conjunto

Dentro del bucle, se utilizan corchetes dobles [[ ]] para acceder a elementos
específicos de la lista y asignarles valores. En este caso, el bucle recorre 
diferentes combinaciones de años (2020 y 2021) y meses (de 4 a 12 para 2020, 
y de 1 a 4 para 2021).


```r
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

La clave del entendimiento está en estas líneas:

```r
tipos_de_datos[[nombre_variable]] <- sapply(get(nombre_variable), class)
```

- `nombre_variable`: Es una cadena que se crea dinámicamente para representar
  el nombre de la variable que se está procesando. Este nombre refleja el
  periodo (año y código) del data frame que se esta analizando.
  
- `get(nombre_variable)`: Se utiliza para obtener el contenido del objeto con el
  nombre almacenado en `nombre_variable`. En este caso, es un data frame.
  
- `sapply(get(nombre_variable), class)`: Se utiliza para aplicar la función
  `class`a cada columna del data frame con nombre `nombre_variable`

Finalmente, este vector de tipos de datos se asigna a un elemento específico 
de la lista tipos_de_datos utilizando `[[nombre_variable]]`, donde 
`nombre_variable` sirve como la clave única para ese elemento en la lista.