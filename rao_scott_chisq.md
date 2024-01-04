Chi Square Test with Complex Survey Design Weights
================
Micah Nelson
2024-1-4

This code example applies the Rao-Scott correction to a chi-square test
to account for complex survey design weights. This example uses American
National Election Studies 2020 data to examine gender differences in
affiliation with the Democratic party

Load necessary packages and read in data. (Note: this dataset has
already been cleaned and variables recoded)

``` r
library(haven)
library(survey)

data <- read_dta("anes_2020_cleaned.dta")
```

Create a survey design object: ids specifies cluster/PSU variable strata
specifies strata variable weights specify sampling weights nest = TRUE
is necessary if clusters within separate strata have the same values

``` r
design <- svydesign(ids = ~V200010c, 
                    strata = ~V200010d, 
                    weights = ~V200010a, 
                    data = data, 
                    nest = TRUE)
```

Run chi square with female and party ID using svychisq() Default
adjustment with this command is Rao-Scott

``` r
chi <- svychisq(~female + dem, design)

chi
```

    ## 
    ##  Pearson's X^2: Rao & Scott adjustment
    ## 
    ## data:  svychisq(~female + dem, design)
    ## F = 46.437, ndf = 1, ddf = 51, p-value = 1.069e-08

Examine expected, observed, and standardized residuals to see where
there are differences that drive significant chi-square result

``` r
round(chi$expected, 2)
```

    ##       dem
    ## female       0       1
    ##      0 1884.18 2069.63
    ##      1 2061.64 2264.55

``` r
round(chi$observed, 2)
```

    ##       dem
    ## female       0       1
    ##      0 2068.81 1885.00
    ##      1 1877.01 2449.18

``` r
round(chi$stdres, 2)
```

    ##       dem
    ## female     0     1
    ##      0  8.13 -8.13
    ##      1 -8.13  8.13
