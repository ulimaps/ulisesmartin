---
title: "Visualizar_nube_de_puntos_en_web_1"
date: 2022-03-26T11:53:12-06:00
draft: false
---
## PotreeConverter y los 3 pasos para visualizar una nube de puntos en el navegador

Usando PotreeConverter o LAStools podemos convertir un archivo LAS a uno que permita ser visto desde un navegador.

[Potree](https://https://github.com/potree/potree/), es un rederizador de nubes de puntos libre y de código abierto y LAStools es una colección de herramientas para tratamiento de nubes de puntos.

### Los tres pasos son:

1. Convertir archivo LAS --> "directorio convertido" con archivos de potree.
2. Contar con un servidor web para ofrecer el contenido.
3. Colocar el "directorio convertido" en directorio www, htdos o donde será servido para acceder a él accesible por el navegador.

A continuación se explica cómo usar PotreeConverter (Método A), para convertir el archivo con estensión "LAS" a un "\<Directorio de Potree>", y en otra publicación cómo usar laspublish (Método B).

#### Método A, Con PotreeConverter:

**1. Convertir archivo LAS --> "directorio convertido" con archivos de potree:**

* Descargar PotreeConverter (https://github.com/potree/PotreeConverter/releases)
* Ejecutar el comando en una terminal, con sigueinte la sintaxis:

{{< highlight cmd>}}
// En su forma más básica:
PotreeConverter.exe <input> -o <outputDir>
// Considerando el método de muestreo: random o poisson disc:
PotreeConverter.exe <input> -o <outputDir> -m random
// Y tomando en cuenta los nombres de directorios de entrada y salida:
.\PotreeConverter.exe C:\pointclouds\data.las -o C:\pointclouds\data_converted
{{< / highlight >}}

**2. Contar con un servidor web para ofrecer el contenido.**

* En el caso de windows podemos usar XAMPP, para más información: https://www.apachefriends.org/index.html

**3. Copiar el contendio en la carpeta donde se sirven los archivos para la web. Como uso XAMPP, sería algo así:**

{{< highlight cmd>}}
copy C:\pointclouds\data_converted C:\xampp\htdocs
{{< / highlight >}}

Ejemplo 1, usando PotreeConverter 1.7:

{{< highlight cmd>}}
C:\PotreeConverter_1.7>PotreeConverter.exe \nubes\odm_georeferenced_model.laz -o \xampp\htdocs\convertido_a_potree_1 -p index
{{< / highlight >}}

{{< figure src="/images/blog/PotreeConverter1-7_ejemplo1.png" title="Ejecución en el cmd de PotreeConverter 1.7" >}}

El resultado es un director de salida "convertido_a_potree_1" con un archivo index.html que será visualizado en el navegador, como se muestra a continuación:

{{< figure src="/images/blog/PotreeConverter1.7_visualizando_index_1.jpg" title="Visualización de nube de puntos en Potree" >}}

Ejemplo 2, eligiendo el formato de salida como archivo LAZ en lugar de BINARY:
{{< highlight cmd>}}
C:\PotreeConverter_1.7>PotreeConverter.exe \nubes\odm_georeferenced_model.laz -o \xampp\htdocs\convertido_a_potree_2 --output-format LAZ -p index
{{< / highlight >}}

Ejemplo 3, usando PotreeConverter 2.1:

{{< highlight cmd>}}
C:\PotreeConverter_2.1_x64_windows>PotreeConverter.exe \nubes\odm_georeferenced_model.laz -o \xampp\htdocs\convertido_a_potree_3 -p index
{{< / highlight >}}

![PotreeConverter2-1_ejemplo1.png](./images/blog/PotreeConverter2-1_ejemplo1.png)

**Sobre el tamaño del archivo resultante:**

107 MB -- archivo original en .LAZ

431 MB -- convertido_a_potree_1 (sin compresióncon PotreeConverter1.7)

167 MB -- convertido_a_potree_2 (con compresión con PotreeConverter1.7)

778 MB -- convertido_a_potree_3 (sin compresión con PotreeConverter2.1)

**Diferencia entre versiones de PotreeConverter 1.7 y 2.1**

* Hasta el día de hoy, miércoles 24 de febrero de 2021, hay dos versiones: 1.7 y 2.1.
* La versión 1.7 parte el archivo en cachitos, por ejemplo, el directorio "r" en: convertido_a_potree_1\pointclouds\index\data\r contiene muchos subdierctios con archivos .bin y .hrc. 
* Versión 2.1, la opción de compresión aún no está disponible.
* La versión 2.1 es de 10 a 50 veces más rápida, al ejecutarse sobre una unidad de estado sólido SSD.
* La versión 2.1 genera tres archivos, en lugar de muchos archivos y directorios, como se muestra a continuación:

{{< highlight cmd>}}
 Directorio de C:\xampp\htdocs\convertido_a_potree_3\pointclouds\index

24/02/2021  09:05 p. m.    <DIR>          .
24/02/2021  09:05 p. m.    <DIR>          ..
24/02/2021  09:05 p. m.           182,050 hierarchy.bin
24/02/2021  09:05 p. m.             2,372 metadata.json
24/02/2021  09:05 p. m.       762,141,695 octree.bin
               3 archivos    762,326,117 bytes
               2 dirs  201,620,078,592 bytes libres
{{< / highlight >}}
#### Método B, con laspublish:

Con el poderosísimo LASTools, proximamente.



