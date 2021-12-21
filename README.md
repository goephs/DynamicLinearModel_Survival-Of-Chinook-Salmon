# DynamicLinearModel_Survival-Of-Chinook-Salmon
From https://cran.r-project.org/web/packages/MARSS/vignettes/UserGuide.pdf Section 16.2

### Example of a univariate DLM

Let’s consider an example from the literature. Scheuerell and Williams (2005) used a DLM to examine the relationship between marine survival of Chinook salmon and an index of ocean upwelling strength along the west coast of
the USA. Upwelling brings cool, nutrient-rich waters from the deep ocean to shallower coastal areas. Scheuerell and Williams hypothesized that stronger upwelling in April should create better growing conditions for phytoplankton,
which would then translate into more zooplankton. In turn, juvenile salmon (“smolts”) entering the ocean in May and June should find better foraging opportunities. 

There are 42 years of data (1964–2005):

data(SalmonSurvCUI)
years <- SalmonSurvCUI[, 1]
TT <- length(years)
# response data: logit(survival)
dat <- matrix(SalmonSurvCUI[, 2], nrow = 1)
