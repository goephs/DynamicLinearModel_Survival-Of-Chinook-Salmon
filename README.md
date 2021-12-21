# DynamicLinearModel_Survival-Of-Chinook-Salmon
Analysis of multivariate time series using the MARSS package

```
Holmes, E. E., M. D. Scheuerell and E. J. Ward. Analysis of multivariate
time-series using the MARSS package. Version 3.11.4. NOAA Fisheries,
Northwest Fisheries Science Center, 2725 Montlake Blvd E., Seattle, WA
98112. DOI: https://doi.org/10.5281/zenodo.5781847
```

From https://cran.r-project.org/web/packages/MARSS/vignettes/UserGuide.pdf Section 16.2

### Example of a univariate DLM

Let’s consider an example from the literature. Scheuerell and Williams (2005) used a DLM to examine the relationship between marine survival of Chinook salmon and an index of ocean upwelling strength along the west coast of
the USA. Upwelling brings cool, nutrient-rich waters from the deep ocean to shallower coastal areas. Scheuerell and Williams hypothesized that stronger upwelling in April should create better growing conditions for phytoplankton,
which would then translate into more zooplankton. In turn, juvenile salmon (“smolts”) entering the ocean in May and June should find better foraging opportunities. 

Install MARSS

```R
install.packages('MARSS')
library(devtools)
# Windows users will likely need to set this
# Sys.setenv('R_REMOTES_NO_ERRORS_FROM_WARNINGS' = 'true')
devtools::install_github("atsa-es/atsalibrary")
```

There are 42 years of data (1964–2005):

```R
library(atsalibrary)
library(MARSS) # location of SalmonSurvCUI data
data(package="MARSS")
# attach dataset
data(SalmonSurvCUI)
years <- SalmonSurvCUI[, 1]
TT <- length(years)
# response data: logit(survival)
dat <- matrix(SalmonSurvCUI[, 2], nrow = 1)
```

