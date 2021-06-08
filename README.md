# discreteModel

## Installation
Use `install_github()` to install this package
```
remotes::install_github("thinhong/discreteModel")
```

## Example
### Simple SIR model without contact
Here we have to define:
* `transitions`: structure of the model or transitions among compartments, for instance S -> I -> R would be defined by `"S -> I"`, `"I -> R"` (note the `""` symbol, this will be read as strings), white space can be ignored here (i.e we can write `"S->I"`)
* `initialValues`: a vector defines initial values of compartments
* `distributions`: distributions of compartments, currently `exponential(rate)`, `gamma(scale, shape)`, and `weibull(scale, shape)` are available
```
library(discreteModel)

transitions <- c(
  "S -> E",
  "E -> I",
  "I -> R"
)

initialValues <- c(
  S = 999,
  E = 0,
  I = 1,
  R = 0
)

distributions <- c(E = weibull(scale = 2, shape = 5), 
                   I = exponential(rate = 1.5))

fmod <- runSim(daysFollowUp = 5000, errorTolerance = 0.01, timeStep = 0.001, 
               transmissionRate = 1.5, infectiousComps = "I", 
               transitions = transitions,
               initialValues = initialValues, distributions = distributions)
```

### Contact between locations:
We now add another parameter `contacts`: a list of contact matrices. If we have contact between locations, `initialValues` and `distributions` will be lists of locations. The name of elements of `initialValues` and `distributions` need to match the dimension names of the matrices.

This script will simulate a SIR model of 2 locations: Ho Chi Minh and Ha Noi.
```
M_location <- matrix(c(0.85, 0.1, 0.1, 0.95), nrow = 2, ncol = 2, 
                     dimnames = list(c("HCM", "HN"), c("HCM", "HN")))

contacts <- list(
  location = M_location
)

transitions <- c(
  "S -> I", 
  "I -> R"
)

initialValues <- list(
  HCM = c(
    S = 999,
    I = 1,
    R = 0),
  HN = c(
    S = 699,
    I = 0,
    R = 0)
)

distributions <- list(
  HCM = c(I = gamma(scale = 2, shape = 5)),
  HN = c(I = weibull(scale = 2, shape = 5))
)

fmod <- runSim(daysFollowUp = 5000, errorTolerance = 0.01, timeStep = 0.001, 
               transmissionRate = 1.5, infectiousComps = "I", 
               contacts = contacts, transitions = transitions,
               initialValues = initialValues, distributions = distributions)
```

If we have more than one type of contact, define the names of elements of `initialValues` and `distributions` with `.`:
```
M_location <- matrix(c(0.85, 0.1, 0.1, 0.95), nrow = 2, ncol = 2, 
                     dimnames = list(c("HCM", "HN"), c("HCM", "HN")))
M_gender <- matrix(c(0.5, 0.75, 0.75, 0.95), nrow = 2, ncol = 2, 
                     dimnames = list(c("M", "F"), c("M", "F")))

contacts <- list(
  location = M_location,
  gender = M_gender
)

transitions <- c(
  "S -> I", 
  "I -> R"
)

initialValues <- list(
  HCM.M = c(
    S = 999,
    I = 1,
    R = 0),
  HCM.F = c(
    S = 699,
    I = 0,
    R = 0),
  HN.M = c(
    S = 999,
    I = 1,
    R = 0),
  HN.F = c(
    S = 699,
    I = 0,
    R = 0)
)

distributions <- list(
  HCM.M = c(I = gamma(scale = 2, shape = 5)),
  HCM.F = c(I = gamma(scale = 2, shape = 5)),
  HN.M = c(I = weibull(scale = 2, shape = 5)),
  HN.F = c(I = weibull(scale = 2, shape = 5))
)

fmod <- runSim(daysFollowUp = 5000, errorTolerance = 0.01, timeStep = 0.001, 
               transmissionRate = 1.5, infectiousComps = "I", 
               contacts = contacts, transitions = transitions,
               initialValues = initialValues, distributions = distributions)
```
