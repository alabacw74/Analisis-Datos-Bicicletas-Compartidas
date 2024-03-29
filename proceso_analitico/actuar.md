# Proceso de análisis: Actuar

Alfredo Aburto
2024-01-05


## Introducción

Esta es nuestra última fase del proceso necesario para resolver una tarea empresarial
usando el análisis de datos presentaremos nuestras recomendaciones a la acción para
lograr los objetivos de la empresa

> Convertir al mayor número de usuarios `casual` en tipo `member`

## Características clave de los tipos de usuario

1. > Un usuario `casual` realiza muchos menos viajes en un periodo de un año que un usuario `member`.
2. > Los usuarios `casual` viajan menos veces pero por mayor tiempo que un usuario `member`.
3. > Los usuarios `casual` aumentan el número de viajes que realizan conforme se acerca el fin de semana, teniendo un máximo los sábados; realizan un uso casi constante de lunes a jueves siendo significativamente menor en comparación con los usuarios `member`. Por otro lado, los usuarios `member` realizan un uso más constante del servicio a lo largo de los días laborales de la semana, aumentando igualmente sus viajes al acercarse el fin de semana, teniendo un mínimo los domingos.
4. > Un usuario `member` utiliza el sistema para suplir una necesidad de 
transporte a sus espacios laborales, mientras que un usuario `casual` tiene un enfoque
más recreativo.
5. > Un usuario `member`realiza viajes con distancias similares a lo largo de la
semana, por otro lado un usuario `casual` tiene mayor variación en sus
recorridos.
6. > Los meses de Junio, Julio y Agosto son los meses con mayor incidencia ciclista
teniendo su máximo en Agosto.

## Recomendaciones

Nuestras recomendaciones giran entorno a la siguiente cuestión:

> ¿Porqué un usuario `casual` desearía convertirse en un `member`?

Es evidente que los usuarios `casual` representan una gran parte de nuestra
población pero sus razones para mantenerse en el uso casual deben ser lógicas
para ellos de lo contrario se volverían `member`. Con lo anterior quiero decir
que posiblemente a los usuarios `casual` no les resulta evidente que volverse
`member` les traería beneficios, por lo que será necesario ayudar a evidenciar 
dichos beneficios.

Como se ha dicho desde el principio, lograr una mayor cantidad de suscripciones 
anuales es el objetivo y se logrará a través de la campaña de marketing. Nuestras
tres recomendaciones puntuales son:

1. > La campaña de marketing debe concientizar a los usuarios sobre los beneficios
que se obtienen con el uso recurrente de la bicicleta no solo como una herramienta 
de recreación, sino como una tecnología que beneficia al medio ambiente, reduce
el tráfico vehicular, en muchos casos reduce el tiempo de traslado y a su vez
favorece la salud.

A los usuarios les gustará saber que en lo que muchas ocasiones se conoce como
tiempo muerto (traslados) pueden ser aprovechados para mejorar su salud mientras
se divierten usando la bicicleta para llegar a sus trabajos.

2. > Crear ofertas en el costo de la suscripción en los meses de Junio, Julio y
Agosto, ya que son los que mayor número de viajes concentran y en donde existe
una mayor área de oportunidad para obtener nuevas suscripciones.

3. > Crear nuevas categorías de suscripción que permitan el uso del sistema de 
préstamo con los mismos beneficios que los suscriptores anuales pero con un espacio
temporal de uso que abarque los fines de semana.

A los usuarios `casual` les gusta viajar en bicicleta los fines de semana, si
se les presenta una oferta que les permita tener los beneficios de una suscripción
cada fin de semana, seguramente obtendríamos más suscriptores en esta nueva modalidad.

Estas tres recomendaciones buscan empatizar con los usuarios `casual` y entender
sus motivaciones para mantenerse en usuarios casuales y no obtener una suscripción
con la finalidad de poder mostrarles información y ofertas que se adecúen a sus
necesidades y preferencias. Sin embargo, existen muchas áreas de oportunidad para
seguir explorando a nuestros usuarios, en la siguiente sección mencionamos algunas.

## Áreas de oportunidad

Nuestro conjunto de datos no contenía información personal sobre las personas que realizaban los viajes, lo cual limitó nuestra capacidad para determinar el número exacto de usuarios únicos. Esta información podría haber sido relevante para comprender mejor el comportamiento individual de los usuarios y ajustar nuestras estrategias de conversión de manera más personalizada.

Para abordar esta limitación en futuras iniciativas, podríamos considerar la implementación de encuestas opcionales para los usuarios, recopilando datos demográficos y preferencias. Esto nos permitiría crear perfiles más detallados y adaptar nuestras estrategias de marketing de manera más específica, maximizando así las posibilidades de conversión.

Además, explorar asociaciones o colaboraciones con otras empresas locales o servicios de transporte podría ser una estrategia adicional. Al vincularnos con entidades externas, podríamos acceder a datos complementarios que enriquecerían nuestra comprensión del comportamiento del usuario y nos proporcionarían información valiosa para mejorar nuestras iniciativas de marketing.

Estamos conscientes de la importancia de la privacidad de los usuarios, y cualquier esfuerzo adicional para recopilar datos personales se llevaría a cabo respetando las regulaciones y normativas vigentes, asegurando la transparencia y el consentimiento adecuado.

## Próximos pasos

Una vez implementada la campaña de marketing, es crucial realizar un seguimiento riguroso de la cantidad de usuarios `casual` que se convierten en `member`. Este análisis post-implementación nos permitirá evaluar el éxito o, en su caso, identificar áreas de mejora en nuestra estrategia.

Adicionalmente, se recomienda la implementación de un sistema robusto de captura de datos utilizando una base de datos que cumpla con los estándares de diseño. La adopción de una estructura de base de datos bien diseñada, con múltiples tablas, proporcionará una organización eficiente y facilitará la gestión de datos a largo plazo.

Para enriquecer nuestro entendimiento de las conversiones, se sugiere la realización de encuestas de retroalimentación a los usuarios que han realizado el cambio de `casual` a `member`. Estas encuestas pueden arrojar luz sobre los motivadores clave que llevaron a la conversión y proporcionarán valiosos insights para futuras estrategias de retención y fidelización.

Al utilizar un modelo de base de datos que sigue buenas prácticas de diseño, garantizamos la integridad y consistencia de los datos, al tiempo que protegemos la privacidad de los usuarios mediante la adecuada gestión de información personal. Esta medida contribuirá a mantener la calidad de los datos a lo largo del tiempo y permitirá realizar análisis más profundos y personalizados en el futuro.

## Preguntas Adicionales y Enfoque Probabilístico

Como parte de nuestra estrategia analítica, es beneficioso explorar ciertas preguntas adicionales desde una perspectiva probabilística, permitiéndonos obtener insights más matizados y realistas. Aquí destacamos algunas áreas clave:

### 1. Comportamiento Diario y Mensual de Usuarios

> **Pregunta:** ¿Cuál es la probabilidad de que un usuario realice un viaje en bicicleta en un día específico o en un mes determinado?

**Enfoque Probabilístico:** Podemos utilizar modelos de series temporales probabilísticas para prever la cantidad esperada de viajes en diferentes días o meses, considerando patrones estacionales, días de la semana y eventos especiales que puedan afectar el uso de bicicletas.

### 2. Conversión de Usuarios Casuales a Miembros Anuales

> **Pregunta:** ¿Cuál es la probabilidad de conversión de un usuario casual a miembro anual después de ser expuesto a la campaña de marketing?

**Enfoque Probabilístico:** Podemos implementar un modelo de conversión probabilístico que considere factores como la efectividad de la campaña, la demografía del usuario y su historial de viajes para estimar la probabilidad de que un usuario casual se convierta en miembro anual.

### 3. Distancia de Viaje Probable

> **Pregunta:** ¿Cuál es la probabilidad de que un usuario recorra una distancia específica en su viaje en bicicleta?

**Enfoque Probabilístico:** Utilizando distribuciones de probabilidad, podemos modelar la variabilidad en las distancias de viaje y calcular la probabilidad de que un usuario seleccione una distancia particular en su recorrido.

### 4. Disponibilidad de Modelos de Bicicletas en Estaciones

> **Pregunta:** ¿Cuál es la probabilidad de que un modelo específico de bicicleta esté disponible en una estación dada?

**Enfoque Probabilístico:** A través de análisis de disponibilidad histórica y patrones de uso, podemos desarrollar modelos probabilísticos para prever la disponibilidad de modelos específicos en estaciones particulares durante ciertos momentos del día o días de la semana.

Adoptar un enfoque probabilístico en estas áreas nos permitirá no solo hacer predicciones más precisas, sino también cuantificar la incertidumbre asociada con nuestras estimaciones, lo cual es crucial para la toma de decisiones informadas.