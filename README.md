## Scripts for workflow

<br>

The repository provides the scripts and exemplarily data of two species (no. 5 *Diplogale hosei*, a species losing habitat, and 49 , a species gaining habitat in the future) to follow the workflow steps provided in Fig. 1 of Struebig et al. (in revision). A dettailed description is also available in the supplementary material. The structure of the repository, i.e. how data, output and scripts are stored, follows the [d6-package] (https://github.com/EcoDynIZW/d6). 
<br>
**data**: contains the folders raw and processed. Under *raw*, spatial layers of the species HSI and threshold maps as well as land cover layers and the protected areas (PAs) are stored. In *cost_layers*, helper layers like the concession map are used to create the restoration cost layers (script 5 and Table S2); the opportunity cost layer was provided by Runting et al. (2105 NatComms). *Processed* contains the output of spatial operations, such as the circuitscape runs, the least-cost-path runs, the node layers per species and the restoration costs per time slice.
<br>
**plots**: contains plots created for the two species mentioned above.
<br>
**scripts**: contains numbered scripts representing the code for the different steps shown in Fig. 1.
<br>

Note that the scripting was started in 2016 under older versions of R. It was built on no longer supported packages such as `raster` and `rgdal`, and the original code does therefore not run any more. Where possible, we adjusted the code to `terra`, but results might differ. In the code, we had to make several adjustments to area size, as some maps were provided in true area projected coordinate reference systems (CRS), like UTM, or sometimes in angular-true geographic CRS like WGS84, where a raster cell size corresponds to 930 * 930 m, and not 1 km2.

<br>

### 1) habitat suitability (HSI) maps
<br>
The script loads the HSI model maps for each species and 3 time slices (2020, 2050, 2080); these are averages of 4 climatic projections each; suitability values range from 0 (unsuitable) to 1 (highly suitable) or respectively 1-1000 saved as integers to save workspace. Details in Struebig et al. 2015 Current Biology, DOI:https://doi.org/10.1016/j.cub.2014.11.067 .

<br>

### 2) threshold maps and nodes per species
<br>
In the previous work (Struebig et al. 15 CurrBiol), binary (0 and 1 values only) threshold maps were calculated for each species, time slice and each of the 4 climatic projections. The single threshold maps for each species and time slice are loaded, and a single majority consensus threshold map created for each species and time slice, i.e. when the sum of the 4 projections was >2, we saved it as suitable area. This area was intersected with the layer of protected areas to select important PA nodes per species and time slice.

<br>

### 3) circuitscape
<br>
The script creates all folders for the output, the circuitscape initialisation (ini) file and calls the circuitscape.exe. We provide the code also for the new Julia-language version. circuitscape was run for all species and all nodes per time slice.

<br>

### 4) least cost path
<br>
Nodes per species and time slice are loaded, and per node a centroid was calculated. If nodes had complex shapes and the centroid was set outside the node, we shifted the centroid back into the closest node. The values for the nodes are set to the maximum of the circuitscape conductance layer, so that protected areas are not avoided (highest conductance). This conductance layer was reverted into a resistance layer (transition layer in package `gdistance`) in the least cost path (LCP) analysis. Resulting lines were clipped with the nodes to only keep lines outside of PAs. 
<br>

### 5 restoration and opportunity cost extracts
<br>
The maps for restoration costs are calculated for each time slice, and the LCP lines intersected with the restoration costs maps, the binary forest maps (to later calculate the % of forest for each LCP), circuitscape layers, the binary threshold maps (to later calculate the % of suitable habitat crossed by the LCP) and the habitat suitability maps for each time slice, and the information stored to each path. This dataset was used to select the 75% species cut-off LCPs in a GIS.
<br>

### 6) LCP summary statistics
<br>
The summary statistics for each path (75% species cut-off) are calculated here and summarised in plots and tables.

