# Multivariate Analysis of Macroinvertebrate Communities

This project investigates how environmental variables and functional traits shape macroinvertebrate distributions using R. It includes Canonical Correspondence Analysis (CCA), Generalized Linear Latent Variable Models (GLLVM), and Fourth Corner Analysis.

![Biplot Example](path/to/your/biplot_image.png)  
*Replace with the actual path to your plot image.*

---

## Installation

### Required R Packages
Install the following R packages:
```R
install.packages(c("mvabund", "gllvm", "dplyr", "tidyr", "vegan", "lmom", 
                   "data.table", "ggplot2", "magrittr", "stringr", "readxl", 
                   "ape", "Hmsc", "corrplot", "gclus"))
```
## Data Files
Place these files in your working directory:
1. environment.ras: Environmental variables (max_depth, pH, conductivity, altitude, etc.).
2. macroinvertebrates.ras: Presence-absence data for 51 macroinvertebrate taxa across 536 sites.
3. traits.ras: Trait data (e.g., feeding strategy, respiration type).

## Analysis Workflow
### 1. Data Preparation
### Load and filter data
```R
env <- readRDS("environment.ras")
macro <- readRDS("macroinvertebrates.ras") %>% semi_join(env, by = "sample_id")
traits <- readRDS("traits.ras")
```
### 2. Canonical Correspondence Analysis (CCA)
```R
### Run CCA
spe.ca <- cca(macro ~ max_depth + temperature + conductivity + pH + cat_area + altitude, data = env)
anova(spe.ca)  # Check model significance
```

### Plot results
```R
plot(spe.ca, scaling = 1, main = "CCA Triplot")
```

### 3. GLLVM with Environmental Variables
### Fit model with 2 latent variables
```R
fit_bin_env <- gllvm(y, x, family = "binomial", formula = ~ temperature + conductivity + altitude)
```

### Ordination plot
```R
ordiplot.gllvm(fit_bin_env, biplot = TRUE, main = "Species-Environment Biplot")
```

### 4. Fourth Corner Analysis (Trait-Environment Interactions)
```R
# Fit model with traits
fit_4th <- gllvm(y, X = x, TR = TR, family = "binomial", num.lv = 2,
                 formula = y ~ (conductivity + temperature + altitude):(ovip_single + resp_single + feed_single))

# Plot interactions
coefplot(fit_4th, cex.ylab = 0.7, mar = c(4, 9, 2, 1))
```

## Key Results
* Environmental Drivers: Conductivity, temperature, and altitude significantly influenced species distributions (CCA, p < 0.05).
* Trait-Environment Interactions:
   * Negative correlation: Altitude vs. shredder feeding strategy.
   * Positive correlation: Conductivity vs. shredder feeding, altitude vs. tegument respiration.
   * Model Performance: Environmental variables explained 17.3% of species covariation (GLLVM).
   * Reproducibility

## Clone this repository.
* Ensure data files (environment.ras, macroinvertebrates.ras, traits.ras) are in the working directory.
* Run the R script in sequential order.

## References
* R packages: gllvm, mvabund, vegan.
