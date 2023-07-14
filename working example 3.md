# Working Example 2: small number of occurrence records

Species Distribution Models (SDMs) utilize observed occurrence localities and environmental data to establish relationships. These models hold significant promise, particularly in poorly understood tropical landscapes with limited biogeographical data (Raxworthy et al., 2003). In these regions, data regarding the distribution of elusive species are often constrained to small samples of observed locations (Graham et al.,2004; Soberon & Peterson, 2004). This limitation stems from factors such as the rarity of certain species, challenges in sampling them, and the absence of electronically accessible data. The effectiveness of SDMs is strongly impacted by the quantity of records employed during their development, with predictions based on larger sample sizes typically yielding superior results compared to those derived from smaller datasets (Pearson et al, 2007).

There are several reasons why model performance declines with smaller sample sizes. Firstly, when data is limited, parameter estimates become more uncertain, and outliers have a greater impact on the analysis. Moreover, the intricate nature of ecological niches often requires a larger number of samples to effectively capture the full range of conditions in which a species exists. Species' responses to environmental gradients can also display skewed or multimodal patterns, emphasizing the need for an adequate sample size to ensure accurate modeling (Wisz et al, 2008). Furthermore, the consideration of interactions among environmental variables increases the number of parameters that need to be estimated, and this complexity grows exponentially with the number of predictor variables (Merow, Smith and Silander, 2003). As a result, intricate relationships and interactions may necessitate larger datasets to achieve reliable results.

In the biomodelos-sdm tool, *we categorize datasets with less than 20 presence localities as small data sets*. For such data, the standard approach is to use k-fold cross-validation, where the number of bins (k) equals the number of occurrence localities (n) in the dataset (Muscarella et al, 2014). It is commonly known as jackknife or leave-one-out partitioning (Hastie et al., 2009). This process is performed using the ENMeval2 package (Pearson et al., 2007; Shcheglovitova & Anderson, 2013) 

## Environmental Data and Ocurrences

If you skip the last section, extract the files inside of the ".zip" folder *example* to the main root folder. It will overwrite the *Bias_file*, *Data*, and *Occurrences* folders, let the process continue if you are asked about. 
+ *Data* folder, you will find environmental variables representing climatic and other factors of current and future scenarios. You will observe two raster files in ".tif" extension by each scenario. The resolution of this layers are 10 km.
+ *Occurrences* folder, you will find three spreadsheets in ".csv" format. Each ".csv" stores occurrence data using three columns called "species", "lon" and "lat". Go to last section for more information. 

In this example, we are going to run a simple ENM of a single species database, a criptic bird species, the Sooty-capped Puffbird or **[Bucco noanamae](https://ebird.org/species/socpuf1)**. This database have a relative small data set because of bias sampling, it means, many occurrences are located in only one location. The information of this species is stored in the "single_species_small_sample.csv". So, load it and after doing, feel free to explore the object call *dataSp*.

```
dataSp <- read.csv("Example/Occurrences/single_species_small_sample.csv")
```

## Running

In this specific example, we are going to use:

```
fit_biomodelos(
  occ = dataSp, col_sp = "species", col_lat = "lat",
  col_lon = "lon", clim_vars = "worldclim", dir_clim = "Data/env_vars/",
  dir_other = "Data/env_vars/other/", method_M = "points_MCP_buffer", dist_MOV = 25,
  proj_models = "M-M", remove_distance = 10, remove_method = "sqkm",
  fc_small_sample = ("l", "q"), beta_small_sample = c(0.5, 1, 1.5, 2)
)

```

Here is a brief explanation of new the arguments. *In order to save space, those revised or equal to the working example 1 will be override.

+ fc_small_sample: string indicating the feature class function to be used in Maxent model calibration.
+ beta_small_sample: value representing the regularization multiplier to be used in Maxent model calibration.

feature classes and beta regularization multiplier are known as tuning MaxEnt models parameters. They are useful to optimize their performance and improve their accuracy in predicting species distributions. MaxEnt is a powerful modeling technique known for its ability to create complex response curves using a variety of features. It offers flexibility in capturing the relationships between predictors and species occurrences. When parametrizing the MaxEnt model, one must select the appropriate "feature" functions and determine the beta multiplier. With a small sample size, there is a higher risk of overfitting, where the model fits the noise or random variations in the data instead of the true underlying patterns. Simple MaxEnt models, with fewer parameters and complexity, are less prone to overfitting as they have less opportunity to capture noise in the data. This helps to ensure that the model generalizes well to new data and provides more reliable predictions. 

MaxEnt employs different types of features classes to capture specific relationships. Linear features (l) ensure that the mean value of a predictor matches the observed mean value, while quadratic features (q) constrain the variance. Product features (p) capture interactions between predictors, threshold features (t) convert predictors into binary variables using a value, and hinge features (h) use linear functions instead of step functions like t features. 

## Advanced: customize features of MAXENT models


## References

Graham, Catherine H; Ferrier, Simon; Huettman, Falk; Moritz, Craig & Peterson, A. Townsend (2004). New developments in museum-based informatics and applications in biodiversity analysis. Trends in Ecology & Evolution, 19(9) 497-503. https://doi.org/10.1016/j.tree.2004.07.006.

Hastie, Trevor & Tibshirani, Robert & Friedman, Jerome. (2009). The Elements of Statistical Learning: Data Mining, Inference, and Prediction, Second Edition (Springer Series in Statistics). 

Merow, C., Smith, M.J. and Silander, J.A., Jr (2013), A practical guide to MaxEnt for modeling species' distributions: what it does, and why inputs and settings matter. Ecography, 36: 1058-1069. https://doi.org/10.1111/j.1600-0587.2013.07872.x

Muscarella, R., Galante, P.J., Soley-Guardia, M., Boria, R.A., Kass, J., Uriarte, M. and R.P. Anderson (2014). ENMeval: An R package for conducting spatially independent evaluations and estimating optimal model complexity for ecological niche models. Methods in Ecology and Evolution.

Osorio-Olvera L., Lira‐Noriega, A., Soberón, J., Townsend Peterson, A., Falconi, M., Contreras‐Díaz, R.G., Martínez‐Meyer, E., Barve, V. and Barve, N. (2020), ntbox: an R package with graphical user interface for modeling and evaluating multidimensional ecological niches. Methods Ecol Evol. 11, 1199–1206. doi:10.1111/2041-210X.13452. https://github.com/luismurao/ntbox

Pearson, Richard & Raxworthy, Christopher & Nakamura, Miguel & Peterson, Andrew. (2007). Predicting species distributions from small numbers of occurrence records: a test case using cryptic geckos in Madagascar. Journal of Biogeography. 34. 102 - 117. 10.1111/j.1365-2699.2006.01594.x. 

Raxworthy, C., Martinez-Meyer, E., Horning, N. et al. Predicting distributions of known and unknown reptile species in Madagascar. Nature 426, 837–841 (2003). https://doi.org/10.1038/nature02205

Shcheglovitova, Mariya & Anderson, Robert. (2013). Estimating optimal complexity for ecological niche models: A jackknife approach for species with small sample sizes. Ecological Modelling. 269. 10.1016/j.ecolmodel.2013.08.011. 

Wisz, M.s & Hijmans, Robert & Li, Jin & Peterson, Andrew & Graham, Catherine & Guisan, Antoine & Elith, Jane & Dudík, Miroslav & Ferrier, Simon & Huettmann, Falk & Leathwick, John & Lehmann, Anthony & Lohmann, Lucia & Loiselle, Bette & Manion, Glenn & Moritz, Craig & Nakamura, Miguel & Nakazawa, Yoshinori & Overton, Jake & Zimmermann, Niklaus. (2008). Effects of sample size on the performance of species distribution models. Diversity and Distributions. 14. 763-773. 


