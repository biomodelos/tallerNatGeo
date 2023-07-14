# Working Example 1:

Species distribution models (SDMs) are computational models used to predict the potential geographic distribution of species based on environmental variables. These models aim to establish relationships between the observed occurrence records of a species and environmental factors, such as temperature, precipitation, elevation, and land cover, for that reason are called correlative models. By analyzing the known occurrences of a species in relation to these environmental variables, SDMs can then generate predictions about where the species is likely to occur in areas where observations are lacking.

SDMs employ various statistical and machine learning techniques to create predictive models. These models can be used to understand the ecological requirements and habitat preferences of species, identify areas of high species richness or biodiversity, assess the potential impacts of climate change on species distributions, and inform conservation and management strategies. The input data for SDMs typically include species occurrence records (presence or absence data) and spatially explicit environmental data layers. The models use these data to characterize the ecological niche of a species and generate maps or spatial predictions of its potential distribution across a given geographic area.

biomodelos-sdm tool is designed to help and automatize this process. Stay tune to understand the initial mechanisms and logic behind this application.

## Environmental Data and Ocurrences

Extract the files inside of the ".zip" folder *example* to the main root folder. It will overwrite the *Bias_file*, *Data*, and *Occurrences* folders, let the process continue if you are asked about. 
+ *Data* folder, you will find environmental variables representing climatic and other factors of current and future scenarios. You will observe two raster files in ".tif" extension by each scenario. The resolution of this layers are 10 km.
+ *Occurrences* folder, you will find three spreadsheets in ".csv" format. Each ".csv" stores occurrence data, the first one is a single species database with column labels "species", "lon" and "lat", the second one is a single species database having a relative small data set because of bias sampling, it means, many occurrences are located in only one location and the last one is a multiple species database (5 species) using identical column names. This is the structure of each one.
 
| species        | ... | lon | lat | ... |
|--------------------------|-----|-----------------|------------------|-----|
| Chlorochrysa nitidissima | ... | 11.1085         | -74.0612         | ... |
| Chlorochrysa nitidissima | ... | 11.1041         | -74.0695         | ... |
| Chlorochrysa nitidissima | ... | 11.1113         | -74.0549         | ... |
| Chlorochrysa nitidissima | ... | 11.1096         | -74.0449         | ... |
| Chlorochrysa nitidissima | ... | 11.1073         | -74.0489         | ... |
| Chlorochrysa nitidissima | ... | 11.1024         | -74.0616         | ... |
| ...                      | ... | ...             | ...              | ... |


In this example, we are going to run a simple ENM of a single species database, a criptic bird species, the Sooty-capped Puffbird or **[Bucco noanamae](https://ebird.org/species/socpuf1)**. So, load the "single_species.csv". After loading, feel free to explore the object call *dataSp*.

```
dataSp <- read.csv("Example/Occurrences/single_species.csv")
```

## Running

Once the species occurrence data and environmental variables are ready, the function `fit_biomodelos()` can be customized and run. In this specific example, we are going to use:

```
fit_biomodelos(
  occ = dataSp, col_sp = "species", col_lat = "lat",
  col_lon = "lon", clim_vars = "worldclim", dir_clim = "Data/env_vars/",
  dir_other = "Data/env_vars/other/", method_M = "points_buffer", dist_MOV = 74,
  proj_models = "M-M", remove_distance = 10, remove_method = "spthin"
)

```

Here is a brief explanation of each argument:

+ occ: The species occurrence database is referred to as dataSp (previously loaded database).
+ col_sp: The column name in the database containing the species information is "species".
+ col_lon: The column name in the database containing the longitude information is "lon".
+ col_lat: The column name in the database containing the latitude information is "lat".
+ clim_vars: The name of the climatic variables is "worldclim". The function will search for these variables in the specified directory path.
+ dir_clim: The climatic directory is located at "Data/env_vars/".
+ dir_other: The non-climatic variables are located in "Data/env_vars/other/".
+ proj_models: The niche models will be calibrated and projected in the accessible area, indicated by "M-M".
+ method_M: makes reference to what metodology is selected to construct the accesible area or M, it is constructed as a buffer around each occurrence point with a buffer according to a movement distance "points_buffer". 
+ dist_MOV: The movement distance used for constructing the accessible area buffer is 74 kilometers. The following image illustrates the accesible area constructed. (point buffer method)[images/points_buffer.PNG]
+ remove_method: management of sample bias through removing occurrences to avoid over-representation of certain environmental features of the more accessible and extensively surveyed areas. In this case the algorithm "(spthin)[https://onlinelibrary.wiley.com/doi/epdf/10.1111/ecog.01132]" is used.
+ remove_distance: distance in kilometers to be used as threshold in the removing spatial duplicates process, 10 kilometers was used to let only one occurrence per pixel.
 

### Try yourself

Try to change the default settings and train an species distribution model using a minimum convex polygon with a buffer of 25 kilometers and remove occurrence data with another method, please go to [fit_biomodelos](vignettes/fit_biomodelos.md) for more information.

Answer
```
fit_biomodelos(
  occ = dataSp, col_sp = "species", col_lat = "lat",
  col_lon = "lon", clim_vars = "worldclim", dir_clim = "Data/env_vars/",
  dir_other = "Data/env_vars/other/", method_M = "polygon_points_buffer", dist_MOV = 25,
  proj_models = "M-M", remove_distance = 10, remove_method = "sqkm"
)
```

## Advanced feature: Checking console messages and working directory folder

Once you run the last script, you would monitor the process in the console (left down in RStudio) and the working directory folder. In the next table we show how the function works. Each row represents a working step (from zero 0 to 6) that is explained in the column "Action in progress" and you will find what messages are displayed in the RStudio console and how your working directory looks.

|Step|Action in progress|Console message|Working folder|
|-|--|-|--|
|0   |Just after running, the routine creates a species folder in the working directory. Inside the last, it sets up a temporary folder for raster files (Temp), occurrences by species, and a log file. The log file is used to save the parameters given to the function and make possible to track and reproduce the modeling process. You are allowed to see the content of the log file at the end of the process.|``` Preparing folders and files ```|![Step0](images/step0.png)|
|1   |Formating records from the database. In a first moment the routine searches missing coordinates or having strange characters. Then, it removes columns not useful for the following steps|``` formating data```|![Step1](images/step1.png)|
|2   |Spatial thinning of occurrence records in a way to diminish the bias sample and make the process more efficient. Here, by default the function uses [spThin](https://cran.r-project.org/web/packages/spThin/spThin.pdf) [clean_dup](https://github.com/luismurao/ntbox/blob/master/R/clean_dup.R), but it can be customized to run [ntbox](https://github.com/luismurao/ntbox/tree/master/R).|```Removing spatial duplicate occurrences database to 10km"```|![Step2](images/step2.png) |
|3   |Constructing research areas or accessible areas in which the algorithm(s) selected will be trained, or projected in current or future scenarios. In this way, *fit_biomodelos* has several options to construct them. Those are called "Interest areas". Please see [fit_biomodelos](vignettes/fit_biomodelos.md) article.|```Constructing interest areas```|![Step3](images/step3.png)   |
|4   |Cropping and masking the environmental variables, either be current or future ones. It also stores them temporally in a folder call M (or G in case of transferring/projecting the model to other areas)|```Processing environmental layers```| ![Step4](images/step4.png) |
|Optional|Cropping and masking the bias layer constructed by the user to accessible area extent|```Processing bias layer```|![Step_optional](images/step_optional.png)   |
|5   |Running algorithms chosen and evaluating them. Supported algorithms include bioclim (from dismo) and Maxent ( BIOMOD2 has been deprecated since version ). In the current version both bioclim setup and Maxent hyperparameters are tuned using dismo for the former and [ENMeval](https://cran.r-project.org/web/packages/ENMeval/index.html) or [Kuenm](https://github.com/marlonecobos/kuenm) for the latter. Bioclim is run only if there are less than six occurrence records of a species, otherwise other algorithm is runned following the next rule: if there are less than 20 occurrence species records a jackknife procedure is performed and runned by Maxent, by the other side the models are tuned using blocks and runned Maxent. Evaluation of models depends on a hierarchical selection of best Partial Roc , Akaike Information Criterion, and the lowest omission rate at user discretion percentile (default 10th).|```Calibrating and evaluating SDM's```|![Step5](images/step5.png)   |
|6   |Ensambling the best models of each algorithm type. A median, coefficient of variation, standard deviation and sum are calculated, those measures are not performed if only one model is selected. 4 threshold-type maps are calculated from the median or unique model: minimum threshold presence, ten threshold percentile, twenty threshold percentile and thirty threshold according to [Biomodelos framework](http://biomodelos.humboldt.org.co/).|```Calculating ensembles```|![Step6](images/step6.png)   |
|7   |Removing temporal files. The final number, class and type of files is controlled by the argument *remove_files*. Please, see documentation.| |   |
|8   |Close log file and ending execution.|```[1] "ok. Chlorochrysa.nitidissima"```|  |

## References

Cobos ME, Peterson AT, Barve N, Osorio-Olvera L. (2019) kuenm: an R package for detailed development of ecological niche models using Maxent PeerJ, 7:e6281 URL http://doi.org/10.7717/peerj.6281

Muscarella, R., Galante, P.J., Soley-Guardia, M., Boria, R.A., Kass, J., Uriarte, M. and R.P. Anderson (2014). ENMeval: An R package for conducting spatially independent evaluations and estimating optimal model complexity for ecological niche models. Methods in Ecology and Evolution.

Peterson, A., Soberón, J., G. Pearson, R., Anderson, R., Martínez-Meyer, E., Nakamura,M., y Araújo, M. (2011) Ecological Niches and Geographic Distributions, 49.  360 pp.

Peterson, A., Soberón, J., G. Pearson, R., Anderson, R., Martínez-Meyer, E., Nakamura,M., y Araújo, M. (2011) Ecological Niches and Geographic Distributions, tomo 49.  360 pp.

Thuiller Wilfried ; Georges Damien; Gueguen Maya; Engler Robin and Breiner Frank (2021). biomod2: Ensemble Platform for Species Distribution Modeling. R package version 3.5.1. https://CRAN.R-project.org/package=biomod2

Zizka A, Silvestro D, Andermann T, Azevedo J, Duarte Ritter C, Edler D, Farooq H, Herdean
A, Ariza M, Scharn R, Svanteson S, Wengstrom N, Zizka V, Antonelli A (2019).
“CoordinateCleaner: standardized cleaning of occurrence records from biological collection
databases.” _Methods in Ecology and Evolution_, -7. doi: 10.1111/2041-210X.13152 (URL:
https://doi.org/10.1111/2041-210X.13152), R package version 2.0-18, <URL:
https://github.com/ropensci/CoordinateCleaner>.



