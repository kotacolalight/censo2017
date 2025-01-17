
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Censo 2017 (Paquete R)

<!-- badges: start -->

[![Project Status: Active – The project has reached a stable, usable
state and is being actively
developed.](https://lifecycle.r-lib.org/articles/figures/lifecycle-stable.svg)](https://lifecycle.r-lib.org/articles/stages.html#stable-1)
[![Lifecycle:
stable](https://img.shields.io/badge/lifecycle-maturing-blue.svg)](https://www.tidyverse.org/lifecycle/#stable)
[![GH-actions](https://github.com/pachamaltese/censo2017/workflows/R-CMD-check/badge.svg)](https://github.com/pachamaltese/censo2017/actions)
[![codecov](https://codecov.io/gh/pachamaltese/censo2017/branch/main/graph/badge.svg?token=XI59cmGd15)](https://codecov.io/gh/pachamaltese/censo2017)
[![CRAN
status](https://www.r-pkg.org/badges/version/censo2017)](https://CRAN.R-project.org/package=censo2017)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4277761.svg)](https://doi.org/10.5281/zenodo.4277761)
<!-- badges: end -->

# Acerca de

Provee un acceso conveniente a más de 17 millones de registros de la
base de datos del Censo 2017. Los datos fueron importados desde el DVD
oficial del INE usando el [Convertidor
REDATAM](https://github.com/discontinuos/redatam-converter/) creado por
Pablo De Grande y además se proporcionan los mapas que acompañan a estos
datos. Estos mismos datos en DVD posteriormente quedaron disponibles en
las [Bases de Datos del
INE](https://www.ine.cl/estadisticas/sociales/censos-de-poblacion-y-vivienda/poblacion-y-vivienda).

Después de la primera llamada a `library(censo2017)` se le pedirá al
usuario que descargue la base usando `censo_descargar_base()` y se puede
modificar la ruta de descarga con la variable de entorno
`CENSO_BBDD_DIR`. La variable de entorno se puede crear con
`usethis::edit_r_environ()`.

El sitio de la documentación es pacha.dev/censo2017 y las viñetas con
ejemplos están disponibles en pacha.dev/censo2017/articles.

# Instalación

Versión estable

    install.packages("censo2017")

Versión de desarrollo

    # install.packages("remotes")
    remotes::install_github("pachamaltese/censo2017")

# Valor agregado sobre los archivos SHP y REDATAM del INE

Esta version de la base de datos del Censo 2017 presenta algunas
diferencias respecto de la original que se obtiene en DVD y corresponde
a una versión DuckDB derivada a partir de los Microdatos del Censo 2017
en formato DVD.

La modificacion sobre los archivos originales, que incluyen geometrias
detalladas disponibles en [Cartografias
Censo2017](https://github.com/pachamaltese/cartografias-censo2017),
consistió en unir todos los archivos SHP regionales en una ínica tabla
por nivel (e.g en lugar de proveer `R01_mapa_comunas`, …,
`R15_mapa_comunas` combiné las 15 regiones en una única tabla
`mapa_comunas`).

Los cambios concretos respecto de la base original son los siguientes:

-   Nombres de columna en formato “tidy” (e.g. `comuna_ref_id` en lugar
    de `COMUNA_REF_ID`).
-   Agregué los nombres de las unidades geográficas (e.g. se incluye
    `nom_comuna` en la tabla `comunas` para facilitar los filtros).
-   Añadí la variable `geocodigo` a la tabla de `zonas`. Esto facilita
    mucho las uniones con las tablas de mapas en SQL.
-   Tambien incluí las observaciones 16054 to 16060 en la variable
    `zonaloc_ref_id`. Esto se debio a que era necesario para crear una
    llave foránea desde la tabla `mapa_zonas` (ver repositorio
    [Cartografias
    Censo2017](https://github.com/pachamaltese/cartografias-censo2017))
    y vincular el `geocodigo` (no todas las zonas del mapa están
    presentes en los datos del Censo).

Además de los datos del Censo, incluí la descripción de las variables en
formato tabla (y no en XML como se obtiene del DVD). La ventaja de esto
es poder consultar rápidamente lo que significan los códigos de
variables y su etiquetado, por ejemplo:

``` r
# con la bbdd instalada
library(censo2017)
library(dplyr)

censo_tabla("variables") %>% 
  filter(variable == "p01")
#> # A tibble: 1 x 5
#>   tabla     variable descripcion      tipo    rango 
#>   <chr>     <chr>    <chr>            <chr>   <chr> 
#> 1 viviendas p01      Tipo de Vivienda integer 1 - 10

censo_tabla("variables_codificacion") %>% 
  filter(variable == "p01")
#> # A tibble: 12 x 4
#>    tabla     variable valor descripcion                                         
#>    <chr>     <chr>    <int> <chr>                                               
#>  1 viviendas p01          1 Casa                                                
#>  2 viviendas p01          2 Departamento en Edificio                            
#>  3 viviendas p01          3 Vivienda Tradicional Indígena (Ruka, Pae Pae u Otra…
#>  4 viviendas p01          4 Pieza en Casa Antigua o en Conventillo              
#>  5 viviendas p01          5 Mediagua, Mejora, Rancho o Choza                    
#>  6 viviendas p01          6 Móvil (Carpa, Casa Rodante o Similar)               
#>  7 viviendas p01          7 Otro Tipo de Vivienda Particular                    
#>  8 viviendas p01          8 Vivienda Colectiva                                  
#>  9 viviendas p01          9 Operativo Personas en Tránsito (No Es Vivienda)     
#> 10 viviendas p01         10 Operativo Calle (No Es Vivienda)                    
#> 11 viviendas p01         11 Valor Perdido                                       
#> 12 viviendas p01          0 No Aplica
```

# Relación de Censo 2017 con Chilemapas

Todos los datos de estos repositorios contemplan 15 regiones pues los
archivos del Censo se entregan de esta forma y este paquete está 100%
orientado a facilitar el acceso a datos.

Por su parte, [chilemapas](https://pacha.dev/chilemapas/) se centra
únicamente en los mapas y también usa las cartografías del DVD del Censo
para entregar mapas simplificados (de menor detalle y más livianos).
Chilemapas cuenta con una transformación de códigos para dar cuenta de
la creacion de la Region de Ñuble.

En resumen, censo2017 permite construir estadísticas demográficas y
chilemapas ayuda a mostrarlas en un mapa usando ggplot2 (u otro paquete
como tmap).

# Cita este trabajo

Si usas `censo2017` en trabajos académicos u otro tipo de publicación
por favor usa la siguiente cita:

    Mauricio Vargas (2020). censo2017: Base de Datos de Fácil Acceso del Censo
      2017 de Chile (2017 Chilean Census Easy Access Database). R package version
      0.1. https://pacha.dev/censo2017/

Entrada para BibTeX:

    @Manual{,
      title = {censo2017: Base de Datos de F\'acil Acceso del Censo 2017 de Chile
    (2017 Chilean Census Easy Access Database)},
      author = {Mauricio Vargas},
      year = {2020},
      note = {R package version 0.1},
      url = {https://pacha.dev/censo2017/},
      doi = {10.5281/zenodo.4277761}
    }

# Contribuciones

Para contribuir a este proyecto debes estar de acuerdo con el [Código de
Conducta](https://pacha.dev/censo2017/CODE_OF_CONDUCT.html). Me es útil
contar con más ejemplos, mejoras a las funciones y todo lo que ayude a
la comunidad. Si tienes algo que aportar me puedes dejar un issue, pull
request o enviarme un tweet (@pachamaltese) si tienes dudas respecto de
cómo aportar.

# Agradecimientos

Muchas gracias a Juan Correa
([@Juanizio\_C](https://twitter.com/Juanizio_C/)) por su asesoría como
geógrafo experto.

# Aportes

Si quieres donar para aportar al desarrollo de este y más paquetes Open
Source, puedes hacerlo en [Buy Me a
Coffee](https://www.buymeacoffee.com/pacha/).
