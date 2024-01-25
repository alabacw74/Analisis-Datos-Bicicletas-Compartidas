# Conjunto de Datos de Cyclistic: Análisis de Usuarios de Bicicletas Compartidas

Alfredo Aburto
2024-01-05

- [Introducción](#introducción)
- [Fuente de Datos](#fuente-de-datos)
- [Fecha de Adquisición](#fecha-de-adquisición)
- [Organización de los Datos](#organización-de-los-datos)
- [Importancia de las variables en nuestro análisis](#importancia-de-las-variables-en-nuestro-analisís)
- [Formato de los Datos](#formato-de-los-datos)
- [Alcance de los Datos](#alcance-de-los-datos)
- [Integridad de los Datos](#integridad-de-los-datos)
- [Limitaciones y Consideraciones Éticas](#limitaciones-y-consideraciones-éticas)
- [Licencia](#licencia)

## Introducción
Se utilizarán los datos históricos de los viajes de Cyclistic para analizar e 
identificar tendencias que permitan solucionar nuestra tarea empresarial:

> Definir en qué se diferencian los socios anuales y los ciclistas ocasionales 
con respecto al uso de las bicicletas de Cyclistic

## Fuente de Datos

El conjunto de datos se obtiene de [divybykes](https://divvybikes.com/system-data).
Estos datos corresponden a **datos de terceros**, son proporcionados por 
Bikeshare, pero los datos son del sistema Divvy propiedad de la ciudad de
Chicago.

## Fecha de Adquisición

Los datos son recopilados el 5 de enero de 2024

## Organización de los Datos

Los datos de cada viaje son anonimizados (proceso realizado por el 
proveedor de datos) e incluyen:

- Día y hora de inicio del viaje
- Día y hora de finalización del viaje
- Estación de inicio del viaje
- Estación final de viaje
- Latitud y Longitud de las estaciones de inicio y finalización del viaje
- Tipo de pasajero (miembro, viaje único y pase de un día)

Los datos se han procesado para eliminar los viajes que realiza el personal 
mientras presta servicio e inspecciona el sistema; y cualquier viaje que dure
menos de 60 segundos (posiblemente salidas en falso o usuarios que intenten
volver a acoplar una bicicleta para asegurarse de que esté segura)

## Importancia de las variables en nuestro análisis

Los datos obtenidos son significativos para nuestro análisis, se resume el
uso que se le dará a cada uno en la siguiente tabla:

| Variable                                     | Descripción de uso                              |
| -------------------------------------------- | ----------------------------------------------- |
| Día y hora de inicio y finalización del viaje | Permite extraer la duración del viaje realizado |
| Estación de inicio y finalización del viaje  | Permite extraer la distancia del viaje          |
| Tipo de pasajero                             | Permite encontrar las características especiales de cada tipo de pasajero al relacionarlo con la información obtenida de las variables anteriores |

## Formato de los Datos

Los datos se obtienen con extensión .zip y al extraerse se obtienen en formato
.csv en formato ancho con columnas claramente definidas.

## Alcance de los Datos

Los datos contienen información de los viajes desde el 1 de junio del 2020 hasta
el 7 de mayo del 2021. Se ha elegido este periodo, pues abarca el periodo de 
un año entero en el que se incluye un cambio de año. En este se espera 
observar tendencias especiales en los meses diciembre - enero.

## Integridad de los datos

En el [Acuerdo de licencia de datos](https://divvybikes.com/data-license-agreement)
se menciona lo siguiente:

> Sin garantía. LOS DATOS SE PROPORCIONAN “TAL CUAL”, SEGÚN DISPONIBILIDAD 
(A DISCRECIÓN EXCLUSIVA DE BIKESHARE) Y BAJO SU PROPIO RIESGO. EN LA MEDIDA 
MÁXIMA DISPUESTA POR LA LEY, BIKESHARE RENUNCIA A TODAS LAS GARANTÍAS, EXPRESAS 
O IMPLÍCITAS, INCLUYENDO LAS GARANTÍAS IMPLÍCITAS DE COMERCIABILIDAD, IDONEIDAD 
PARA UN PROPÓSITO PARTICULAR Y NO INFRACCIÓN. BIKESHARE ADEMÁS NIEGA CUALQUIER 
GARANTÍA DE QUE LOS DATOS SATISFARÁN SUS NECESIDADES O ESTARÁN O CONTINUARÁN 
DISPONIBLES, COMPLETOS, EXACTOS, OPORTUNOS, SEGUROS O LIBRES DE ERRORES.

Esto se interpreta en el sentido de que los datos pueden necesitar de un 
procesamiento para garantizar que los datos cumplan con los estándares 
necesarios para nuestro análisis, sin embargo, también se menciona que los datos
son propiedad del estado de Chicago y se ponen a disposición del público con el
fin de apoyar el transporte alternativo, por lo anterior se espera que los datos
sigan un perfil profesional que cumpla con los objetivos del estado.

## Limitaciones y Consideraciones Éticas

Estos datos son recopilados con un fin académico, ya que se utiliza para 
desarrollar un proyecto de finalización como parte de la formación de analista
de datos de Google. Por ningún motivo se busca extraer datos para correlacionar
datos personales u obtener beneficios económicos a partir de dicho proyecto.

## Licencia

Lyft Bikes and Scooters, LLC (“Bikeshare”) opera el servicio de bicicletas 
compartidas Divvy de la ciudad de Chicago (“Ciudad”). Bikeshare y la Ciudad 
están comprometidos a apoyar el uso de la bicicleta como una opción de 
transporte alternativa. Como parte de ese compromiso, la Ciudad permite que 
Bikeshare ponga a disposición del público ciertos datos del sistema Divvy 
propiedad de la Ciudad (“Datos”), sujeto a los términos y condiciones de este
 Acuerdo de licencia (“Acuerdo”). Al acceder o utilizar cualquiera de los 
 Datos, usted acepta todos los términos y condiciones de este Acuerdo.

Se puede consultar la licencia en [Acuerdo de licencia de datos](https://divvybikes.com/data-license-agreement)

**Nota**: Los conjuntos de datos tienen un nombre diferente a Cyclistic, ya que
esta es una empresa ficticia para fines de este caso práctico.
