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

As we have seen in other chapters, standardizing our covariate(s) to have zero-mean and unit-variance can be helpful in model fitting and interpretation. In this case, it is a good idea because the variance of CUI.apr is orders of magnitude greater than survival.

```R
CUI <- SalmonSurvCUI[, "CUI.apr"]
CUI.z <- zscore(CUI)
# number of state = # of regression params (slope(s) + intercept)
m <- 1 + 1
```

Next, we need to set up the appropriate matrices and vectors for the
MARSS() function. Let’s begin with those for the process equation because
they are straightforward.

```R
# for process eqn
B <- diag(m) # 2x2; Identity
U <- matrix(0, nrow = m, ncol = 1) # 2x1; both elements = 0
Q <- matrix(list(0), m, m) # 2x2; all 0 for now
diag(Q) <- c("q1", "q2") # 2x2; diag = (q1,q2)

# for observation eqn
Z <- array(NA, c(1, m, TT)) # NxMxT; empty for now
Z[1, 1, ] <- rep(1, TT) # Nx1; 1's for intercept
Z[1, 2, ] <- CUI.z # Nx1; regr variable
A <- matrix(0) # 1x1; scalar = 0
R <- matrix("r") # 1x1; scalar = r
# only need starting values for regr parameters
inits.list <- list(x0 = matrix(c(0, 0), nrow = m))
# list of model matrices & vectors
mod.list <- list(B = B, U = U, Q = Q, Z = Z, A = A, R = R)
```

And now we can fit our DLM with the MARSS() function.

```R
dlm1 <- MARSS(dat, inits = inits.list, model = mod.list)
```

```text
Success! abstol and log-log tests passed at 115 iterations.
Alert: conv.test.slope.tol is 0.5.
Test with smaller values (<0.1) to ensure convergence.

MARSS fit is
Estimation method: kem 
Convergence test: conv.test.slope.tol = 0.5, abstol = 0.001
Estimation converged in 115 iterations. 
Log-likelihood: -40.03813 
AIC: 90.07627   AICc: 91.74293   
 
      Estimate
R.r    0.15708
Q.q1   0.11264
Q.q2   0.00564
x0.X1 -3.34023
x0.X2 -0.05388
Initial states (x0) defined at t=0

Standard errors have not been calculated. 
Use MARSSparamCIs to compute CIs and bias estimates.
```
