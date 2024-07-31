## Scripts for workflow

<br>

The repository provides the scripts and exemplarily data of two species (no. 5 *Diplogale hosei*, a civet losing habitat, and no. 49 *Pteropus vampyrus*, a bat species gaining habitat in the future) to follow the workflow steps provided in Fig. 1 of Struebig et al. (in revision). A detailed description is also available in the supplementary material. The structure of the repository, i.e. how data, output and scripts are stored, follows the [d6-package] (https://github.com/EcoDynIZW/d6). 
<br>

**data**: contains the folders raw and processed. Under *raw*, spatial layers of the species HSI and threshold maps as well as land cover layers and the protected areas (PAs) are stored. In *cost_layers*, helper layers like the concession map are used to create the restoration cost layers (e.g., script 5 and Table S2); the opportunity cost layer was provided by Runting et al. (2105 NatComms). *Processed* contains the output of spatial operations, such as the circuitscape runs, the least-cost-path runs, the node layers per species and the restoration costs per time slice.
<br>
**plots**: contains plots created for the two species mentioned above.
<br>
**scripts**: contains numbered scripts representing the code for the different steps shown in Fig. 1.
<br>

Note that the scripting was started in 2016 under older versions of R. It was built on no longer supported packages such as `raster`, `rgeos`, `rgdal` and `sp`, and the original code does therefore not run any more. Where possible, we adjusted the code to `terra` and `sf`, but results might differ. In the code, we had to make several adjustments to area size, as some maps were provided in true area projected coordinate reference systems (CRS) like UTM, or sometimes in angular-true geographic CRS like WGS84, where a raster cell size corresponds to 930 * 930 m, and not 1 km2. 
The numbered scripts contain the following codes for the steps:

<br>

### 1) habitat suitability (HSI) maps
The script loads the HSI model maps for each species and 3 time slices (2020, 2050, 2080); these are averages of 4 climatic projections each that incorporate suitability of land use and human population density; suitability values range from 0 (unsuitable) to 1 (highly suitable) or respectively 1-1000 when converted to integers to save workspace. Details in Struebig et al. 2015 Current Biology, DOI:https://doi.org/10.1016/j.cub.2014.11.067 .

<br>

### 2) threshold maps and nodes per species
In the previous work (Struebig et al. 15 CurrBiol), binary (0 and 1 values only) threshold maps were calculated for each species, time slice and each of the 4 climatic projections. The four threshold maps for each species and time slice are loaded, and a single majority consensus threshold map created for each species and time slice, i.e. when the sum of the 4 projections was >2, we kept it as suitable area. This area was intersected with the layer of protected areas (PA) to select important PAs per species and time slice.

<br>

### 3) circuitscape
The script creates all folders for the output, the circuitscape initialisation (ini) file and calls the circuitscape.exe. We provide the code also for the new Julia-language version. Circuitscape was run for all species and all nodes per time slice and layers stored. The HSI maps were used as conductance layers, and the PAs as nodes.

<br>

### 4) least cost path
Nodes per species and time slice are loaded, and per node a centroid was calculated. If nodes had complex shapes and the centroid laid outside the node, we shifted the centroid back into the node at the closest distance. The raster values for the nodes were set to the maximum of the circuitscape current flow layer, so that protected areas are never avoided (least cost). This updated current flow layer was reverted into a resistance layer (transition layer in package `gdistance`) in the least cost path (LCP) analysis. Resulting lines were clipped with the nodes to only further analyse lines outside of PAs. 

<br>

### 5 restoration and opportunity cost extracts
The maps for restoration costs were calculated for each time slice, and the LCP lines intersected with the restoration costs maps, the binary forest maps (to later calculate the % of forest for each LCP), circuitscape layers, the binary threshold maps (to later calculate the % of suitable habitat crossed by the LCP for each species) and the habitat suitability maps of each species for each time slice, and the information stored to each path. This dataset was then used to select the 75% species cut-off LCPs in a GIS.

<br>

### 6) LCP summary statistics
The summary statistics for each path (75% species cut-off) are calculated here and summarised in plots and tables.

