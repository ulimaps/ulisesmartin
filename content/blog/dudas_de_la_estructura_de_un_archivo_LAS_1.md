---
title: "Dudas_de_la_estructura_de_un_archivo_LAS_1"
date: 2022-03-14T21:50:43-06:00
draft: false
---
 
Comparando un archivo de una nube de puntos proveniente de DJI Terra y otro archivo luego de hacerle un filtro en pdal (filters.outlier) veo que hay una diferencia entre el factor de escal y el offset.

El filtro en un archivo "outlier_statistical.json" sería este:

[
    "cloud_78c2634818850f1.las",
    {
        "type":"filters.outlier",
        "method":"statistical",
        "mean_k":12,
        "multiplier":2.2
    },
    "cloud_78c2634818850f1_noised.las"
]

La ejeución seria:

$ pdal pipeline .\outlier_statistical.json

Entonces lasinfo con -histo intensity 16 -histo z 5:


| cloud_78c2634818850f1.las                                                                                                          | loud_78c2634818850f1_noised.las                                                                                                               | Duda |
|------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|------|
| lasinfo -i f:\nubes\tren\cloud_78c2634818850f1.las -otxt -odix _con_histo -odir f:\nubes\tren\info\ -histo intensity 16 -histo z 5 | lasinfo.exe \nubes\tren_maya\cloud_78c2634818850f1_noised.las -otxt -odix _con_histo -odir f:\nubes\tren\info\ -histo intensity 16 -histo z 5 |      |
| scale factor x y z:         0.0001 0.0001 0.0001                                                                                   | scale factor x y z:         0.01 0.01 0.01                                                                                                    | 1    |
| offset x y z:               483415.6290059014 2278015.6279708305 46.799277914468881                                                | offset x y z:               0 0 0                                                                                                             | 2    |
| min x y z:                  482956.0910 2277610.7170 14.5594                                                                       | min x y z:                  482956.09 2277610.72 14.56                                                                                        |      |
| max x y z:                  483875.1670 2278420.5389 79.0391                                                                       | max x y z:                  483875.17 2278420.54 79.04                                                                                        |      |
|                                                                                                                                    |                                                                                                                                               |      |
| variable length header record 1 of 3:                                                                                              | variable length header record 2 of 2:                                                                                                         |      |
| reserved             43707                                                                                                         | reserved             0                                                                                                                        |      |
|                                                                                                                                    |                                                                                                                                               |      |
| reporting minimum and maximum for all LAS point record entries ..                                                                  | reporting minimum and maximum for all LAS point record entries ..                                                                             |      |
| X            -4595380    4595380                                                                                                   | X            48295609   48387517                                                                                                              | 3    |
| Y            -4049110    4049110                                                                                                   | Y           227761072  227842054                                                                                                              | 4    |
| Z             -322399     322399                                                                                                   | Z                1456       7904                                                                                                              | 5    |
| gps_time 325359530.048202 325360725.767503                                                                                         | gps_time 325359530.048202 325360725.767503                                                                                                    |      |
| -                                                                                                                                  | WARNING: range violates GPS week time specified by global encoding bit 0                                                                      | 6    |
| histogram of classification of points:                                                                                             | histogram of classification of points:                                                                                                        |      |
| 229136701  never classified (0)                                                                                                    | 221520924  never classified (0)                                                                                                               |      |
| -                                                                                                                                  | 7615777  noise (7)                                                                                                                            |      |
|                                                                                                                                    |                                                                                                                                               |      |
| WARNING: real max y larger than header max y by 0.000032                                                                           | WARNING: real max x larger than header max x by 0.002994                                                                                      | 7    |
| WARNING: real min y smaller than header min y by 0.000032                                                                          |                                                                                                                                               |      |
| WARNING: real max z larger than header max z by 0.000031                                                                           |                                                                                                                                               |      |
| WARNING: real min z smaller than header min z by 0.000031                                                                          |                                                                                                                                               |      |

En ambos casos ocurre algo como esto:

WARNING: stored resolution of min_x not compatible with x_offset and x_scale_factor: 482956.09102576884
WARNING: stored resolution of min_y not compatible with y_offset and y_scale_factor: 2277610.7170027224
WARNING: stored resolution of min_z not compatible with z_offset and z_scale_factor: 14.559409263034066
WARNING: stored resolution of max_x not compatible with x_offset and x_scale_factor: 483875.1669860339
WARNING: stored resolution of max_y not compatible with y_offset and y_scale_factor: 2278420.5389389391
WARNING: stored resolution of max_z not compatible with z_offset and z_scale_factor: 79.039146565903692

Duda 1.- ¿Porqué el scale factor disminuye? ¿Habría que indicarle la precisión de una décima de milímietro para que se conserve? 

Duda 2.- ¿Porqué igualmente se disminuye el offset al grado de hacerse cero?

Duda 3.- ¿Qué pas+o aquí?

Duda 4.- ¿Qué es esto de "WARNING: range violates GPS week time specified by global encoding bit 0"?

Duda 5.- ¿Qué significa esto de real max x larger than header max v by 0.002994, y los demás valores?

Lo iré resolviendo.

