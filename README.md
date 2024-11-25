# README: The Effect of Health Risk on Housing Values: Evidence from a Cancer Cluster

This project uses data from Lucas Davis' 2004 paper titled **"The Effect of Health Risk on Housing Values: Evidence from a Cancer Cluster"**, published in the *American Economic Review*. The paper investigates the effect of a child cancer cluster in Churchill County on housing prices, estimating the willingness of residents to pay to avoid environmental health risks. The goal of this project is to replicate the analysis using data from real estate transactions in two counties: Churchill and Lyons.

## Project Overview

This repository contains R scripts that clean, merge, and analyze housing price data, as well as generate summary statistics and estimate the effect of the cancer cluster on home sales. The analysis includes:

- Data cleaning and merging
- Construction of new variables such as the sale year, square footage, age of the house, and log-transformed sales prices
- Difference-in-differences estimation to measure the impact of the cancer cluster on housing prices
- Regression analysis and interpretation of results
- Summary statistics and visualizations

## Setup and Installation

1. Clone this repository to your local machine.
2. Install the necessary R packages by running the following in your R environment.
3. Load the R scripts into RStudio or your preferred R environment. The scripts can be run sequentially to replicate the analysis from the paper.

## Data Description

### 1. **Data Files:**
- `cc.dta`: Contains home sales records for Churchill County (pre-cancer cluster data)
- `lc.dta`: Contains home sales records for Lyons County (pre-cancer cluster data)
- `lc2.dta`: Contains additional home sales records for Lyons County (post-cancer cluster data)
- `cc2.dta`: Contains additional home sales records for Churchill County (post-cancer cluster data)
- `price.dta`: Contains the Nevada Home Price Index (NVHPI) used to adjust real sales prices


### Variables:
- `parcel`: Parcel identification number for the property.
- `date`: Sale date in YYYYMMDD format.
- `usecode`: Code indicating the type of property (residential, commercial, etc.).
- `sales`: Sale price of the home.
- `acres`: Number of acres of the property.
- `sqft`: Square footage of the home.
- `constryr`: Year the house was constructed.
- `cc`: Indicator variable for Churchill County (1 if Churchill, 0 if Lyons).
- `lc`: Indicator variable for Lyons County (1 if Lyons, 0 if Churchill).
- `sale_yr`: The year of the sale.
- `sale_mm`: The month of the sale.
- `sale_day`: The day of the sale.
- `age`: The age of the home (current year minus construction year).
- `log`: Log-transformed nominal sale price.
- `q`: Quarter of the year when the sale occurred.
- `Post`: Indicator variable for the years after 1999, when the cancer cluster emerged.

## Methodology

### Difference-in-Differences (DID) Estimation

The core analysis uses a **Difference-in-Differences (DID)** approach to estimate the impact of the cancer cluster on housing prices. The DID approach compares the change in housing prices in Churchill County (the treatment group) before and after the cancer cluster emergence, relative to Lyons County (the control group), which is assumed to be unaffected.

The model is specified as follows:

```math
log(sale\_price)_{it} = \beta_0 + \beta_1 \text{Post}_t + \beta_2 \text{cc}_i + \beta_3 Post_t cc_i + \epsilon_{it}
```

Where:
- `log(sale_price)`: Log-transformed sales price of the home.
- `Post`: Indicator variable for years after 1999.
- `cc`: Indicator variable for Churchill County.
- `Post * cc`: Interaction term capturing the differential effect on Churchill County post-cancer cluster.
- `epsilon`: Error term.

### Data Adjustments

The sales prices are adjusted for inflation using the Nevada Home Price Index (`nvhpi`) available for each quarter. The real sales prices are calculated as:

```math
\text{adjusted\_sales\_price} = \frac{\text{sales\_price} \times 100}{\text{nvhpi}}
```

## Results

### Regression Results:
- The regression analysis provides insights into the effect of the cancer cluster on housing prices, particularly in Churchill County after the emergence of the cancer cluster in 2000.
- Key coefficients from the DID regressions are interpreted to assess how prices in Churchill County diverged from Lyons County after the cancer cluster began.

### Summary Statistics:
- Summary statistics are generated to compare housing characteristics (such as sale price, square footage, acres, and age) between the two counties before the cancer cluster emerged.

### Visualizations:
- Plots illustrating the trend of average home prices over time in both counties are provided, with confidence intervals for the estimates.

## Assumptions

For the results to be considered causal, the following assumptions must hold:
- **Parallel Trends Assumption**: Without the cancer cluster, the housing prices in Churchill County would have followed the same trend as in Lyons County. The only difference in trends after 1999 is due to the cancer cluster.

## Conclusion

This project replicates Lucas Davis' 2004 study on the effect of a cancer cluster on housing prices. By implementing a Difference-in-Differences approach, we estimate the causal effect of the cancer cluster on housing prices and provide supporting visualizations and statistics.

## License

This repository is licensed under the MIT License. See the LICENSE file for more details.

## References

- Davis, L. W. (2004). The Effect of Health Risk on Housing Values: Evidence from a Cancer Cluster. *American Economic Review, 94*(5), 1692–1704.
- Lucas W. Davis, *The Effect of Health Risk on Housing Values: Evidence from a Cancer Cluster*, American Economic Review, 2004.
