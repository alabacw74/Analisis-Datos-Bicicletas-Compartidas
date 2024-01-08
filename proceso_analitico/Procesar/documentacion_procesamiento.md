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

## Paso 3: Comparación de Tipos de datos

Luego de obtener los tipos de datos de cada columna de cada data frame, se 
realiza una comparación para verificar si todas las columnas tienen el mismo 
tipo de dato en todos los data frames.

```r
comparacion_tipos <- all(sapply(tipos_de_datos[-1],
                                function(x) identical(x, 
                                                      tipos_de_datos[[1]])))
```

Vamos a analizar el código paso a paso:

```R
comparacion_tipos <- all(sapply(tipos_de_datos[-1], function(x) identical(x, tipos_de_datos[[1]])))
```

1. **`sapply(tipos_de_datos[-1], function(x) identical(x, tipos_de_datos[[1]]))`:**
   - `tipos_de_datos[-1]`: Esta parte toma todos los elementos de `tipos_de_datos` excepto el primer elemento. Aquí, el `-1` indica que se excluya el primer elemento.
   - `function(x) identical(x, tipos_de_datos[[1]])`: Aquí se define una función anónima que toma un argumento `x` y verifica si es idéntico al primer elemento de `tipos_de_datos`. La función `identical(x, tipos_de_datos[[1]])` devuelve `TRUE` si `x` es idéntico al primer elemento de `tipos_de_datos`; de lo contrario, devuelve `FALSE`.
   - `sapply(...)`: Aplica la función definida a cada elemento de `tipos_de_datos[-1]`. Esto crea un vector lógico que indica si cada elemento es idéntico al primer elemento o no.

2. **`all(...)`:**
   - `all(...)` devuelve `TRUE` si todos los elementos del vector lógico son `TRUE`; de lo contrario, devuelve `FALSE`. En este caso, significa que todos los elementos de `tipos_de_datos[-1]` son idénticos al primer elemento de `tipos_de_datos`.

3. **`comparacion_tipos`:**
   - `comparacion_tipos` es la variable que almacena el resultado final de la comparación. Si todos los elementos de `tipos_de_datos[-1]` son idénticos al primer elemento, `comparacion_tipos` será `TRUE`; de lo contrario, será `FALSE`.

La intención es comparar todos los elementos de `tipos_de_datos` con respecto 
al primer elemento. La razón por la cual se utiliza `tipos_de_datos[-1]` 
(todos los elementos de `tipos_de_datos` excepto el primer elemento) es porque 
el primer elemento se compara con sí mismo. Si incluimos el primer elemento en 
la comparación, obtendríamos siempre `TRUE` porque cualquier elemento es 
idéntico a sí mismo.

Al usar `tipos_de_datos[-1]`, excluimos el primer elemento de la comparación, 
y luego comparamos cada elemento restante con el primer elemento. Esto nos da 
una comparación más significativa, ya que estamos interesados en ver si hay
 alguna diferencia entre los tipos de datos de los elementos a partir del segundo.

`tipos_de_datos[-1]` se utiliza para excluir el primer elemento de
la comparación y evitar la trivialidad de comparar un elemento consigo mismo.
Queremos comparar todos los elementos subsiguientes con respecto al primer 
elemento para identificar posibles diferencias en los tipos de datos.

Desglosemos la expresión `function(x) identical(x, tipos_de_datos[[1]])`:

1. `function(x)`: Define una función anónima en R con un parámetro `x`. 
Esta función toma un argumento `x` y realiza alguna operación.

2. `identical(x, tipos_de_datos[[1]])`: Dentro de la función anónima, se 
utiliza la función `identical` para verificar si `x` es idéntico al primer 
elemento de `tipos_de_datos`. La función `identical` devuelve `TRUE` si los 
objetos son idénticos y `FALSE` en caso contrario.

Entonces, en términos sencillos, la función anónima se encarga de comparar un 
objeto (`x`) con el primer elemento de `tipos_de_datos`. Esta función se utiliza
 dentro de `sapply` para aplicarla a cada elemento de `tipos_de_datos` y 
 verificar si cada elemento es idéntico al primer elemento.

## Paso 4: Identificación de columnas diferentes

La sección `else` del código se ejecuta cuando la condición `comparacion_tipos` 
es falsa, es decir, cuando al menos un data frame tiene columnas con diferentes
 tipos de datos. Vamos a analizar detalladamente esta sección:

```r
if (comparacion_tipos) {
  print("Todos los data frames tienen las mismas columnas con el 
        mismo tipo de dato.")
} else {
  print("Hay columnas con diferentes tipos de datos en los 
        siguientes data frames:")
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

```r
else {
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

1. **Mensaje de impresión inicial:**
   ```r
   print("Hay columnas con diferentes tipos de datos en los siguientes data frames:")
   ```
   Aquí simplemente estamos imprimiendo un mensaje que indica que hay columnas 
   con diferentes tipos de datos.

2. **Bucle `for` para iterar sobre los data frames con diferentes tipos:**
   ```r
   for (i in 2:length(tipos_de_datos)){
   ```
   Se inicia un bucle `for` que itera desde el segundo elemento hasta el 
   último de la lista `tipos_de_datos`. La iteración comienza desde el 
   segundo elemento (`i = 2`) porque ya hemos comparado el primer elemento 
   con los demás.

3. **Condición `if` para identificar columnas con diferentes tipos de datos:**
Voy a desglosar con más detalle los pasos 3 y 4 del código:

```r
if (!all(sapply(seq_along(tipos_de_datos[[1]]),
              function(j) identical(tipos_de_datos[[i]][[j]], 
                                    tipos_de_datos[[1]][[j]])))) {
```

#### Explicación detallada:

- `sapply(seq_along(tipos_de_datos[[1]]), function(j) ...)`:
  - `seq_along(tipos_de_datos[[1]])` genera una secuencia de números del 1 al 
  número de columnas en el primer data frame (`tipos_de_datos[[1]]`).
  - `sapply` aplica una función (definida por la función anónima) a cada 
  elemento de esta secuencia. En este caso, `j` representa el índice de la
  columna.

- `function(j) identical(tipos_de_datos[[i]][[j]], tipos_de_datos[[1]][[j]])`:
  - Esta es una función anónima que compara los tipos de datos en la columna `j`
   del data frame actual (`tipos_de_datos[[i]]`) con el tipo de dato en la misma
    columna del primer data frame (`tipos_de_datos[[1]]`) usando `identical`.

  - `identical(x, y)` devuelve `TRUE` si `x` e `y` son idénticos, es decir, si 
  tienen el mismo tipo de dato y los mismos valores.

- `all(...)`:
  - La función `all` devuelve `TRUE` si todos los elementos en el vector o lista
   son `TRUE`. En este caso, verifica si todas las columnas tienen los mismos 
   tipos de datos.

- `!all(...)`:
  - El operador `!` invierte la lógica. Por lo tanto, `!all(...)` devuelve 
  `TRUE` si al menos una columna tiene tipos de datos diferentes.


4. **Identificación de columnas con diferentes tipos de datos y mensaje de impresión**

```r
columnas_diferentes <- names(tipos_de_datos[[i]])[!(
  sapply(seq_along(tipos_de_datos[[1]]),
         function(j) identical(tipos_de_datos[[i]][[j]],
                               tipos_de_datos[[1]][[j]]))]
```
Si la condición `if` es verdadera, se identifican las columnas con diferentes
tipos de datos y se almacenan en `columnas_diferentes`. Estas columnas se 
determinan utilizando `names` y filtrando aquellas para las cuales al menos 
una comparación es falsa.

#### Explicación detallada:

- `names(tipos_de_datos[[i]])`:
  - `names` devuelve los nombres de las columnas del data frame actual 
    (`tipos_de_datos[[i]]`).

- `sapply(seq_along(tipos_de_datos[[1]]), function(j) ...)`
  - Esto es similar al paso 3, generando una secuencia de índices de columna y 
    aplicando la función anónima de comparación de tipos.

- `!(...)`:
  - El operador `!` nuevamente invierte la lógica, por lo que `(sapply(...)...)` 
    devuelve `TRUE` si al menos una columna tiene tipos de datos diferentes. 
    `!(sapply(...))` devuelve `TRUE` si todas las columnas tienen los mismos 
    tipos de datos.

- `names(...)[!(...)]`:
  - Esto extrae los nombres de las columnas que tienen tipos de datos 
    diferentes, utilizando la lógica inversa de la comparación.

- `columnas_diferentes <- ...`:
  - Almacena los nombres de las columnas con tipos de datos diferentes en la
    variable `columnas_diferentes`.


5. **Impresión del mensaje final sobre las columnas con diferentes tipos:**
   ```r
   print(paste("En el data frame", names(tipos_de_datos)[i], "las columnas
               siguientes tienen tipos de datos diferentes:", 
               paste(columnas_diferentes, collapse = ", ")))
   ```
Finalmente, se imprime un mensaje detallando en qué data frame específico 
(`names(tipos_de_datos)[i]`) y qué columnas (`columnas_diferentes`) tienen
 diferentes tipos de datos.

Esta sección del código identifica y presenta de manera detallada las columnas 
con diferentes tipos de datos en los data frames.
