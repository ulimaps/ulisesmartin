---
title: "Medir desplazamientos horizontales y verticales en nubes de puntos LIDAR 1"
date: 2021-09-07T15:15:54-05:00
draft: false
---
**¿Sabes cómo medir los desplazamientos horizontales y verticales en nubes de puntos LIDAR?** A continuación te platico la primer parte de un procedimiento para hacerlo. 

***Dudas, asesorías, cursos y presupuestos, por favor contáctame por mis redes sociales que arriba se listan o visita la página de mi despacho [geoytopo.com](http://geoytopo.com)***

**Planteamiento**: Cuando se escanéa con un sistema de mapeo móvil montado en un drone que tiene un movimiento planeado en líneas de vuelo, el LIDAR radía pulsos láser que se traducirán en una nube de puntos. Si se radía o mapea un mismo lugar u objeto desde dos líneas de vuelo contiguas: ¿Cuál sería la diferencias o desfase entre puntos pertenecientes a un mismo objeto mapeado desde dos líneas de vuelo diferentes?

**Objetivo General**: Cuantificar el desplazamiento horizontal y vertical entre puntos que definen un elemento.

**Objetivo particular**: Realizar la primer parte del procedimiento: Partir un archivo de nubes de puntos en formato "LAS" en archivos más pequeños que contengan una sola dirección de vuelo para definir las líneas de vuelo.

Las líneas de vuelo que conseguí se muestran en la siguiente figura, separada cada uno por un color:

{{< figure src="/images/blog/museo_despues_partir_lineas1.png" title="Cada color es una líneas de vuelo" >}}

El siguiente procedimiento viene de revisar:

* El blog de LAStools donde Martín Isenburg, el creador de LAStools, explica cómo comparar las diferencias entre los valores de elevaciones más bajas entre dos líneas de vuelo. Aquí el blog: [Strip Aligning of Drone LiDAR flown with Livox MID-40 over destroyed Mangrove](https://rapidlasso.com/2021/03/04/strip-aligning-of-drone-lidar-flown-with-livox-mid-40-over-destroyed-mangrove/). 

* El Texto de las [especificaciones del arcivo LAS versión 1.4 revisión 13](https://www.asprs.org/news/press-releases/press-release-archives/las-1-4-specification-approved-by-asprs-board.html). Ya hay revisiones más nuevas. El texto en pdf en este enalce: https://www.asprs.org/wp-content/uploads/2010/12/LAS_1_4_r13.pdf

Los datos de mi ejercicio los obtuve de un colega Topógrafo que además es Arquitecto, gracias Arq. Dante Loaeza.

La *metodología para lograr el objetivo general* de cuantificar la diferencia vertical entre puntos de un mismo elemento pero mapeado desde dos líneas de vuelo contigua es el siguiente:

* Cortar un archivo, llamado archivo.LAS en pequeños archivos, uno por cada línea de vuelo.
* Obtener la densidad de puntos por pixel, por ejemplo 50 (cm) X 50 (cm) que pasaría a ser una especie de unidad de área.
* Cuantificar la diferencia vertical máxima entre los puntos más bajos en áreas mapeadas en común por más de una línea de vuelo. 
* Ilustrar la diferencia: 
	* Menor a 10 (cm) el área de coloreará en blanco
	* Entre 10 cm y 25 cm área color rojo
	* Mayor a 25 cm área color azul.
	* Ajustar el desalineamiento entre líneas de vuelo. Esto no se hace con LAStools, si no con [BayesStripAlign](http://bayesmap.com/products/bayesstripalign/) de BayesMap Solutions.

La *metodología para lograr el objetivo particular* de cortar un archivo LAS en líneas de vuelo, justo lo que haré en esta entrada al blog, y es la siguiente:

0. Contar con un archivo que al visualizarse se desarrolle de forma progresiva, coincidiendo con las líneas de vuelo a la hora de hacer el levantamiento.
1. Visualizar la nube de puntos.
2. Obtener información sobre el archivo2.LAS
3. Obtener información de tiempo gps o gps_time, de un punto inicial y un punto final de la línea de vuelo o flight line, es decir conocer los intervalos de tiempo gps de las líneas de vuelo.
4. Generar un archivo por cada línea de vuelo, del tipo "Línea_De_Vuelo_NúmeroX.LAS", por medio de la extracción de puntos del archivo2.LAS que fueron capturados en cierto intervalo de tiempo gps, tiempo inicial y tiempo final, lo que definiría cada la línea de vuelo. Y también decirle a los nuevos archivos que son una línea de vuelo con un número identificador único para este levantamiento. Y esto no sólo se hace dándoles un nombre distinto a los archivos, si no diciéndole a cada punto que conforma este archivo que tiene un "archivo de origen". La forma de decirle al punto de donde vino es otorgarle un número al un campo de cada puntos que se llama "origen del punto" o point_source, y este número será justamente la línea de vuelo. Entonces: "archivo de origen" = "origen del punto". 

Y es todo, es sencillo, ¿no?

**Nota: Seguramente hay formas más sencillas pero esta es la que hasta ahorita he encontrado.**

*Desarrollo de la metodología:*

0. Contar con un archivo.LAS.

Tuve dos archivos: archivo_1.LAS y archivo_2.LAS. El archivo_1.LAS estaba procesado de tal forma que al visualizarlo se iba desplegando un pedacito por aquí y luego otro por allá. En cambio el archivo_2.LAS iba desplegando visualmente de forma progresiva, coincidiendo con el progreso del levantamiento realizado, es decir que coincidía con las líneas de vuelo. Entonces, el archivo2.LAS es el que necesitamos.

1. Visualizar la nube de puntos.

{{< highlight powershell >}}
C:\LAStools\bin>lasview.exe \nubes\terra_las\cloud_ea164a9.las
{{< /highlight >}}

{{< figure src="/images/blog/museo_antes_partir_lineas1.png" title="Visualización de puntos antes de partir archivo en líneas de vuelo" >}}

2. Obtener información sobre el archivo2.LAS y enviar el resultado a un archivo de texto para revisarlo tranquilamente:

{{< highlight powershell >}}
C:\LAStools\bin>lasinfo \nubes\terra_las\cloud_ea164a9.las -otxt -odix _informe -odir \nubes\terra_las\
{{< /highlight >}}

Y nos da un archivo txt, llamado: cloud_ea164a9_informe.txt. Ahí se puede ver hasta abajo una tabla como la que sigue:

  intensity           0        255

  return_number       1          3

  number_of_returns   1          3

  edge_of_flight_line 0          0

  scan_direction_flag 0          0

  classification      0          0

  scan_angle_rank   -35         35

  user_data           0          0

  point_source_ID     0          0

  gps_time 310218303.145946 310219073.269431


¿Y eso qué nos dice?  Primero, si observamos el rango o intervalo de valores del penúltimo campo "point_source_ID", vemos que va de cero a cero (0 a 0). Y como arriba expliqué el "point_source" y el "file_source" están relacionados para indicar de donde viene el punto y con esto también indicar las líneas de vuelo. ¿Que cómo sé esto? Pues lo averigüé leyendo un poquito las especificaciones de los formatos de archivo LAS, como a continuación podrás ver.

En la página 6 de la especificación "LAS_1_4_r13.pdf" se leé:

**File Source ID (ID del Archivo de Origen o ID del Archivo Fuente):** Este campo debe establecerse en un valor comprendido entre el rango de 0 a 65,535. Si este archivo se derivó de una línea de vuelo original, este campo suele ser el número de línea de vuelo. Se interpreta un valor de cero (0) como que no se ha asignado una ID. En este caso, el software de procesamiento es libre de asignar un número. Note que este esquema permite que un proyecto LIDAR contenga hasta 65,535 fuentes únicas. Una fuente (File Source) puede ser una línea de vuelo original o puede ser el resultado de operaciones de combinación y/o extracción.

Y en la página 12 se leé:

**Point Source ID (ID de la Fuente del Punto):** Este valor indica desde cual archivo se originó este punto. Los valores válidos para este campo son 1 a 65,535, incluso puede usarse cero para un caso especial discutido abajo. El valor numérico corresponde para el campo File Source ID desde el cual se originó el punto. El cero se está reservado como un convención de los implementadores del sistema. Un Point Source ID de zero implica que este punto fue originado en este archivo. Esto implica que el software de procesamiento debería establecer el Point Source ID igual que el File Source ID del archivo que contiene este punto en algún momento durante el procesamiento.

Nuevamente: **"Esto implica que el software de procesamiento debería establecer el "Point Source ID" igual que el "File Source ID" del archivo que contiene este punto en algún momento durante el procesamiento."**

En otras palabras, los puntos que provienen de un archivo tendrían un identificador "Point Source ID" con un valor igual al identificador del archivo del cual proviene "File Source ID". Y cuando este valor es igual a cero "0" implicaría que el punto se generó en este archivo y que fue generado por el software de procesamiento. Algo como si fuera el "Archivo Primario o Base o Primitivo o Madre o Padre o el archivo dosito, el origen de todo". Y que a partir este archivo primitivo pueden generarse nuevos archivos que al poneres un valor de numérico de 1 a 65,535 como identificadores del origen del punto, le estaremos estableciendo a su vez un identificador para cada línea de vuelo.

3. Obtener información sobre el tiempo gps incial y final de cada línea de vuelo:

Un punto contenido en un archivo.LAS además de tener información sobre sus coordenadas, su point_source_ID también guarda información sobre su gps_time (tiempo GPS).

Para identificar las líneas de vuelo, hay que visualizar repetidamente la nube de puntos observar cómo se va desarrollando cada línea de vuelo. Se puede hacer usando lasview con la opción "-load_gps_time", para que nos permita visualizar información sobre el tiempo gps de cada punto del cual queramos saberlo. De las siguiente forma:

{{< highlight powershell >}}
C:\LAStools\bin>lasview.exe \nubes\terra_las\cloud_ea164a9.las -load_gps_time
{{< /highlight >}}

{{< figure src="/images/blog/museo_buscando_tiempo_gps_en_lineas_de_vuelo1.png" title="Buscando tiempo gps para definir líneas de vuelo" >}}

Luego con el puntero del mouse y presiona do la tecla "i" se mostrará información sobre el punto, incluyendo el tiempo gps. Lo haremos del inicio y final de cada línea de vuelo para averiguar los intervalos de tiempo:

tiempo gps inicial - tiempo gps final

310218303 - 310218390

310218390 - 310218490

310218490 - 310218590

310218590 - 310218690

310218690 - 310218790

310218790 - 310218890

310218890 - 310218990

310218990 - 310219074

**Nota: ¿Hay una forma más elegante de hacerlo? Si, obteniendo una grafica de densidad georreferenciada con LAStools, como más adelante explicaré, luego obtener las coordenadas de principio y fin de las líneas de vuelo que pueden inferirse por los surcos que deja la diferencia de densidad. Posteriormente habría que usar pdal y buscar un puntos cercanos a estos puntos que encontramos, que caigan dentro de un círculo concéntrico o una vecindad y elegir el punto que tenga tiempo gps cercano a lo que buscamos. Es una posibilidad.**

4. Generar un archivo por cada línea de vuelo. Esto es, generar archivos por extracción de puntos desde archivo2.LAS, usando el argumento que extraiga de todos los puntos que se encuentren en un intervalo de tiempo gps. También establecer un identificador de origen del punto = 1, 2, 3, 4, según la línea de vuelo de la que se trate. Para esto se usa la herramienta de LAStools llamada *lassplit*. 

*Nota: es muy importante hacerlo con pdal. Pronto pdal será el pan de cada día porque está muy cerca de QGis.*

*Nota: si parce poco elegante no hacer un script, lo siento, no se me ha dando esa facilidad por usar el shell o el commad del msdos. Así que lo haré de archivo en archivo.*

**¿Qué hace o qué está diciendo esta línea de comando? Dice:** parte el archivo cloud_ea164a9.las generando a partir de él un archivo nuevo, en este archivo nuevo vas a guardar los puntos capturados en un tiempo mayor que 310218303 y menor que 310218390, luego a cada punto establece que su "Point Source ID" sea igual a 1, 2, 3,...,n, luego al archivo que resulte le veas a poner un sufijo de "_cut", después lo vas a guardar con la extensión "laz" y el archivo lo vas a poner dentro del directorio: \nubes\terra_las\split\. 

*Nota: recordatorio, above es encima y below es debajo. Y así para cada uno de los intervalos*

{{< highlight powershell >}}

C:\LAStools\bin>lassplit -i \nubes\terra_las\cloud_ea164a9.las ^
-keep_gps_time_above 310218303 -keep_gps_time_below 310218390 ^
-set_point_source 1 -odix _cut -olaz -odir \nubes\terra_las\split

C:\LAStools\bin>lassplit -i \nubes\terra_las\cloud_ea164a9.las ^
-keep_gps_time_above 310218390 -keep_gps_time_below 310218490 ^
-set_point_source 2 -odix _cut -olaz -odir \nubes\terra_las\split

...
...
...

C:\LAStools\bin>lassplit -i \nubes\terra_las\cloud_ea164a9.las ^
-keep_gps_time_above 310218990 -keep_gps_time_below 310219074 ^
-set_point_source 8 -odix _cut -olaz -odir \nubes\terra_las\split

{{< /highlight >}}


Y listo, ya deben de estar las líneas de vuelo separadas en archivos .LAZ independientes, dentro de la carpeta destino, sonrientes y esperando por nosotros para ser visualizadas cual perritos alegres, tiernos y meneando sus colitas de gusto. :) 

Para cerciorarse de que todo está como debiera, se usa nuevamente el comando  "lasview", se le da click segundario y se elige la opción de rendererizar sólo por "flight time", y debería a aparecernos lo que se muestra en la imagen de aquí abajito:

{{< figure src="/images/blog/museo_despues_partir_lineas1.png" title="Buscando tiempo gps para definir líneas de vuelo" >}}

Bueno, se me quedaron los ojitos rojitos de tanto estar en la compu pero ha valido la pena. Aclaro que nada inventé yo, que sólo trato de explicarlo al explićarmelo a mi mismo. 

Gracias por leer hasta aquí, si tienen dudas pueden contactarme por medio de las redes sociales cuya dirección se muestra en la parte superior de la página. Si gustas un curso de algún tema que te interese y yo conozca sólo contáctame y nos ponemos de acuerdo.

Afortunadamente mucha gente como Martín Isenburg de LAStools, o Howard Buttler de pdal y una cantidad enorme de gente inteligente y generosa nos comparten su conocimiento y esfuerzo y con ello nos permiten darnos un banquete. Por mi parte, considero que la mejor forma de honrar este esfuerzo es estudiando y compartiendo en lengua mexicana sus saberes.

Saludos a mis colegas y a mi cría que nunca va a leer esto, y a mi novia que si lo leé según yo, y a la Sra. Ernestina, besito a todos :°.

Postre y sugerencia: leer todo de nuevo, escuchando música tristecita, como Morrissey o un tal Ed Maverick u otro muchacho llamado Kevin Kaarl o McEnroe. La tristeza sin ningún sentido más allá de la absurda tristeza existencial misma, ayuda a entender mejor las nubes de puntos, tienen mi palabra, prueben. Escuchen esta poderosa rola de Mauri Tapia de Los Retos, chulada: {{< youtube id="VRQDOFaFqWk" title="Los Retros - Someone To Spend Time With" >}} 

Buen provecho.

