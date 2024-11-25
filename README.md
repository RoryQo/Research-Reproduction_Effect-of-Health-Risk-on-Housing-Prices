<h1 align="center">The Effect of Health Risk on Housing Values: Evidence from a Cancer Cluster</h1>



<h2 align="center">Table of Contents</h2>
<table align="center">          
  <tr>  
    <td colspan="2" align="center"><strong>Table of Contents</strong></td>  
  </tr> 
  <tr>  
    <td>1. <a href="#project-overview">Project Overview</a></td>   
    <td>5. <a href="#visualizations">Visualizations</a></td>
  </tr>
  <tr> 
    <td>2. <a href="#data-description">Data Description</a></td>
    <td>6. <a href="#conclusion">Conclusion</a></td> 
  </tr>
  <tr>
    <td>3. <a href="#data-cleaning">Data Cleaning</a></td>
    <td>7. <a href="#references">References</a></td>
  </tr>
  <tr> 
    <td>4. <a href="#methodology">Methodology</a></td>
    <td></td>
  </tr>
  <tr>
    <td colspan="2">
      <ul>
        <li><a href="#difference-in-differences-did-estimation">Difference-in-Differences (DID) Estimation</a></li>
        <li><a href="#inflation-adjustments">Inflation Adjustments</a></li>
      </ul>
    </td>
  </tr>
  <tr>
    <td colspan="2">
      <ul>
        <li><a href="#home-prices-over-time">Home Prices over Time</a></li>
        <li><a href="#event-study-analysis">Event Study Analysis</a></li>
      </ul>
    </td>
  </tr>
</table>


## Project Overview

This project uses data from Lucas Davis' 2004 paper titled **"The Effect of Health Risk on Housing Values: Evidence from a Cancer Cluster"**, published in the *American Economic Review*. The paper investigates the effect of a child cancer cluster in Churchill County on housing prices, estimating the willingness of residents to pay to avoid environmental health risks. The goal of this project is to replicate the analysis using data from real estate transactions in two counties: Churchill and Lyons.

The data can be found by following the link on the AER’s website which will take you to the ICPSR’s data
repository, or in this repositories data folder.

[![View Original Research Paper](https://img.shields.io/badge/View%20Original%20Research%20Paper-0056A0?style=flat&logo=external-link&logoColor=white&color=0056A0)](https://www.aeaweb.org/articles?id=10.1257/0002828043052358)


## Data Description

The dataset contains the following columns:
- `sales`: The sales price of the home.
- `sale_yr`: The year the house was sold.
- `cc`: County identifier (Churchill or Lyon).
- `home_type`: Type of home (single-family, multi-family, etc.).

### Key Variables for the Analysis:
- `sale_yr` will be used to identify the timeline surrounding the cancer cluster event (year 2000).
- `cc` will help us differentiate between Churchill and Lyon counties.

## Data Cleaning

Before proceeding with the analysis, we clean the dataset:
1. Remove missing or invalid values.
2. Convert columns to appropriate data types (e.g., `sale_yr` to integer).
3. Filter the data to only include home sales between 1995 and 2005 (around the cancer cluster event).
4. Create indicator variable for Churchill county

```
temp1<-temp1[!is.na(temp1$date),]
temp1<-temp1[temp1$usecode==20,]
temp1<-temp1[temp1$date<=20001300,]

# generate two new variables: a Churchill county indicator, cc and a Lyon County indicator, lc.
temp1$cc<-1
temp1$lc<-0
```

## Methodology

### Difference-in-Differences (DID) Estimation

The core analysis uses a **Difference-in-Differences (DID)** approach to estimate the impact of the cancer cluster on housing prices. The DID approach compares the change in housing prices in Churchill County (the treatment group) before and after the cancer cluster emergence, relative to Lyons County (the control group), which is assumed to be unaffected.

The model is specified as follows:

```math
log(sale\_price)_{it} = \beta_0 + \beta_1 \text{Post}_t + \beta_2 \text{cc}_i + \beta_3 Post_t cc_i + \epsilon_{it}
```

```math
sale\_price_{it} = \beta_0 + \beta_1 \text{Post}_t + \beta_2 \text{cc}_i + \beta_3 Post_t cc_i + \epsilon_{it}
```

Where:
- `log(sale_price)`: Log-transformed sales price of the home.
- `Post`: Indicator variable for years after 1999.
- `cc`: Indicator variable for Churchill County.
- `Post * cc`: Interaction term capturing the differential effect on Churchill County post-cancer cluster.
- `epsilon`: Error term.

### Inflation Adjustments

The sales prices are adjusted for inflation using the Nevada Home Price Index (`nvhpi`) available for each quarter. The real sales prices are calculated as:

```math
\text{adjusted\_sales\_price} = \frac{\text{sales\_price} \times 100}{\text{nvhpi}}
```
```
nvhpi<-read_dta("price.dta")
nvhpi<-as.data.frame(nvhpi)

tempn <- merge(temp, nvhpi[, c("year", "quarter", "nvhpi")], 
              by.x = c("sale_yr", "q"), 
              by.y = c("year", "quarter"), 
              all.x = TRUE)

tempn$adj_index<- (tempn$sales*100)/tempn$nvhpi
```


### Key Models:
1. **Regression Model**: This model includes county, year, and other factors as independent variables to predict home prices.

- The regression analysis provides insights into the effect of the cancer cluster on housing prices, particularly in Churchill County after the emergence of the cancer cluster in 2000.

  <div style="display: flex; align-items: center;">
    <img src="https://github.com/RoryQo/Reproducing-Research_Effect-of-Health-Risk-on-Housing-Prices/blob/main/Figures/Diff-Diff.jpg?raw=true" width="500px" style="order: 2;"/>
    <div style="margin-left: 20px;">
        <p><strong>β<sub>0</sub>:</strong> Houses in the control group (Lyon County) have an average log(house price) of 11.519</p>
        <p><strong>β<sub>1</sub>:</strong> After the treatment, the log(house price) increases by approximately 23.1%</p>
        <p><strong>β<sub>2</sub>:</strong> Being in the treatment group (Churchill County) is associated with a 4.8% decrease in log real sales</p>
        <p><strong>β<sub>3</sub>:</strong> The combined effect of Treatment and Churchill County reduces log real sales by approximately 7.6%</p>
    </div>
</div>

<br>

2. **Difference-in-Differences (DiD)**: We apply the DiD methodology to analyze if the cancer cluster event had a differential impact on home prices in Churchill County relative to Lyon County.

- Key coefficients from the DID regressions are interpreted to assess how prices in Churchill County diverged from Lyons County after the cancer cluster began.

  <img src="https://github.com/RoryQo/Reproducing-Research_Effect-of-Health-Risk-on-Housing-Prices/blob/main/Figures/PrePost.jpg?raw=true" width=600px style="order: 1;" />


## Visualizations

- Plot illustrating the trend of average home prices over time in both counties are provided, with confidence intervals for the estimates.
- Plot illustrating the estimated effect of Event Study


### Home Prices over Time

The first visualization tracks the trend of home prices over time for both Churchill and Lyon counties. We observe if there was any significant price deviation around the year 2000, which corresponds to the identified cancer cluster event.

<img src="https://github.com/RoryQo/Reproducing-Research_Effect-of-Health-Risk-on-Housing-Prices/blob/main/Figures/AVGHousePrice.jpg?raw=true" width=600px style="display: inline-block;" />

### Event Study Analysis

We perform an event study to analyze the impact of the cancer cluster event in 2000 on home prices. We define two periods: Pre-Event (before 2000) and Post-Event (after 2000). The key steps are:

1. **Define the Event Window**: Homes are categorized based on their sale year (pre and post 2000).
2. **Calculate Abnormal Returns**: We compare the mean sales price of homes before and after the event for both counties.
3. **Plot Results**: We display the mean home prices with confidence intervals to visually identify any significant changes.

   <img src="https://github.com/RoryQo/Reproducing-Research_Effect-of-Health-Risk-on-Housing-Prices/blob/main/Figures/EstEffect.jpg?raw=true" width=500px style="display: inline-block;" />

## Conclusion

The analysis provides insights into the possible effects of the cancer cluster on home prices. By using regression models, the Difference-in-Differences approach, and an event study, we can conclude whether or not the cancer cluster had a significant impact on housing prices in Churchill County compared to Lyon County.

### Future Work:
- Investigating additional variables (e.g., distance from the cancer cluster) might help refine the results.
- Expanding the sample to include more counties in control and treatment groups.

## References

Davis, Lucas W. “The Effect of Health Risk on Housing Values: Evidence from a Cancer Cluster.” American Economic Review, vol. 94, no. 5, Nov. 2004, pp. 1693–1704, https://doi.org/10.1257/0002828043052358.
