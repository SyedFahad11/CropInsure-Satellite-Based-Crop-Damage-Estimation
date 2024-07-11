# Enhancing Crop Insurance with Remote Sensing

## Problem

Natural calamities such as floods and droughts pose significant threats to agricultural productivity worldwide. Traditional crop insurance models often struggle with timely and accurate damage assessment, leading to:
- Delayed compensation
- Financial strain for farmers
- A substantial gap between risk exposure and effective protection

## Solution

Remote sensing data has the potential to revolutionize crop insurance models. This project explores how remotely sensed data can be leveraged to enhance crop damage assessment. We focus primarily on flood and drought damage detection using index insurance.

By addressing the data gap and providing valuable insights, this project aims to:

* Streamline the claims process, reducing delays in compensation
* Improve the accuracy of damage estimation
* Enhance risk assessment and pricing models for insurance providers
* Increase farmers' confidence in crop insurance programs

### Index Insurance

**Index insurance (or parametric insurance)** is a novel type of insurance that is increasingly being advanced in data-poor environments due to its scalability and low cost. Index insurance contracts use observational data of a hazard (e.g., station or satellite-based rainfall, temperature, wind, or flood measurements) to assess hazard frequency and magnitude over time and create a damage index of that hazard. Thresholds are set on this index at various exceedance probabilities to trigger predetermined payouts based on expected financial losses. This methodology reduces transaction costs compared to traditional insurance—which requires individual assessment of each household—and thereby enables affordable premiums accessible to uninsured and low-income populations.

# Part-1: Sentinel-1 Flood Detection Algorithm

## Overview

This algorithm detects floodwater using Sentinel-1 satellite data, which is particularly useful for applications like flood index insurance. The algorithm leverages a time series analysis approach with z-score-based change detection, and it includes spatial smoothing to enhance accuracy.

## Steps

### 1. Baseline Selection
- A historical baseline period of 45 days is selected, representing the minimum soil moisture time series.
- This baseline helps distinguish between regular water bodies and floodwater.

### 2. Time Series Implementation
- Calculate the mean and standard deviation for each pixel over the baseline period for both VV and VH polarization bands.
- For each subsequent image, calculate the z-score for VV and VH bands as follows:
  * $Z_{VV} = \frac{t_{VV} - \mu_{VV}}{\sigma_{VV}}$
  * $Z_{VH} = \frac{t_{VH} - \mu_{VH}}{\sigma_{VH}}$
  * where $t$ is the target image pixel value, $\mu$ is the mean, and $\sigma$ is the standard deviation from the baseline period.

### 3. Pixel Classification
- A pixel is classified as flooded if:
  * $Z_{VV} < -2$ or $Z_{VH} < -2$ or $VH < -22$

### 4. Spatial Smoothing
- Apply spatial smoothing using a kernel with a 30-meter radius.
- A pixel is considered flooded if the mean of the kernel exceeds 0.6.

## Satellite Data

The algorithm uses various satellite datasets:

- **Sentinel-1**: Polar-orbiting SAR satellites providing VV and VH polarizations. Images are preprocessed to remove thermal noise, calibrate radiometry, and correct terrain.
- **NASA-USDA Global Soil Moisture**: Provides soil moisture measurements at 0.25° x 0.25° resolution with a 3-day temporal frequency.
- **MODIS**: Used for detecting surface water through indices like EVI and LSWI.
- **PlanetScope**: High-resolution daily imaging at 3 m resolution used for additional validation.

## References

* [IEEE Reference Paper for index based flood detection](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=10042166)
* [Sentinel-1 Dataset](https://osf.io/tgrx4/)

# Part-2: Establishment of a Comprehensive Drought Monitoring Index

## Overview

The Comprehensive Drought Monitoring Index (CDMI) integrates multiple drought-related factors to provide a holistic view of agricultural drought. The index combines six key indicators using Principal Component Analysis (PCA) to calculate the CDMI, offering a comprehensive assessment of drought conditions.

## Steps

### 1. Normalized Remote Sensing Data

The following indexes are used to capture various aspects of drought:

1. **Vegetation Condition Index (VCI)**:
   * Measures vegetation stress using NDVI (Normalized Difference Vegetation Index).
   * Formula: $VCI = \frac{NDVI - NDVI_{\text{min}}}{NDVI_{\text{max}} - NDVI_{\text{min}}}$
   * Range: 0 (extremely low) to 1 (optimal vegetation conditions).

2. **Temperature Condition Index (TCI)**:
   * Monitors temperature-related drought phenomena using LST (Land Surface Temperature).
   * Formula: $TCI = \frac{LST_{\text{max}} - LST}{LST_{\text{max}} - LST_{\text{min}}}$
   * Range: 0 (extremely low) to 1 (optimal temperature conditions).

3. **Evapotranspiration Condition Index (ETCI)**:
   * Assesses crop water deficit using ET (Evapotranspiration).
   * Formula: $ETCI = \frac{ET_{\text{max}} - ET}{ET_{\text{max}} - ET_{\text{min}}}$
   * Range: 0 (extremely low) to 1 (optimal evapotranspiration conditions).

4. **Precipitation Condition Index (PCI)**:
   * Provides meteorological drought information using TRMM (Tropical Rainfall Measuring Mission) data.
   * Formula: $PCI = \frac{TRMM - TRMM_{\text{min}}}{TRMM_{\text{max}} - TRMM_{\text{min}}}$
   * Range: 0 (extremely low) to 1 (highest precipitation).

5. **Soil Moisture Condition Index (SMCI)**:
   * Characterizes soil moisture levels.
   * Formula: $SMCI = \frac{SM - SM_{\text{min}}}{SM_{\text{max}} - SM_{\text{min}}}$
   * Range: 0 (extremely low) to 1 (optimal soil moisture conditions).

6. **Fluorescence Condition Index (FCI)**:
   * Reflects photosynthesis changes using SIF (Solar-Induced Chlorophyll Fluorescence).
   * Formula: $FCI = \frac{SIF - SIF_{\text{min}}}{SIF_{\text{max}} - SIF_{\text{min}}}$
   * Range: 0 (extremely low) to 1 (optimal fluorescence conditions).

### 2. Principal Component Analysis (PCA)

PCA is applied to combine the six indexes into the CDMI. The steps include:

1. **Data Transformation**: Convert correlated variables into uncorrelated principal components.
2. **Covariance Matrix Calculation**: Determine the covariance matrix of the input data.
3. **Eigenvalues and Eigenvectors**: Compute the eigenvalues and eigenvectors of the covariance matrix.
4. **Principal Components**: Calculate new variables by multiplying the eigenvectors with the original data.

The first principal component, which usually contains over 75% of the information from all indexes, is used as the new drought index, the CDMI.

The CDMI is used to classify agricultural drought into four levels:

1. **Severe Drought**
2. **Moderate Drought**
3. **Light Drought**
4. **No Drought**

The CDMI is calculated and analyzed for the maize growing season (June to September) in Henan Province from 2000 to 2018. The drought levels are determined using quantile thresholds, providing a detailed assessment of long-term agricultural drought and its frequency.

## Datasets Used

This study leverages a range of datasets to comprehensively monitor agricultural drought. The datasets include various remote sensing and meteorological data sources:

1. **Vegetation Index Data**:
   - **NDVI (Normalized Difference Vegetation Index)**: MODIS provides monthly NDVI products (MOD13A3) with a spatial resolution of 1 km, covering the period from 2000 to 2018.
   - [Link to data](https://ladsweb.nascom.nasa.gov/search)

2. **Land Surface Temperature Data**:
   - **LST (Land Surface Temperature)**: MODIS provides 8-day LST products (MOD11A2) with a spatial resolution of 1 km. Monthly LST results are calculated using the maximum value synthesis method, covering the period from 2000 to 2018.
   - [Link to data](https://ladsweb.nascom.nasa.gov/search)

3. **Evapotranspiration Data**:
   - **ET (Evapotranspiration)**: Actual ET (ETa) is generated using the simplified surface energy balance (SSEBop) model. Monthly ET data with a spatial resolution of 0.25° x 0.25° were downloaded.
   - [Link to data](https://github.com/zhangfeng0826/ETdata)

4. **Meteorological Data**:
   - **TRMM (Tropical Rainfall Measuring Mission) Precipitation Data**: Monthly TRMM precipitation data (3B43) were obtained with a spatial resolution of 0.25° x 0.25°, covering the period from 2000 to 2018.
   - **SPEI (Standardized Precipitation Evapotranspiration Index)-01 Data**: Used to analyze meteorological drought from 2000 to 2018.
   - [Link to data](http://disc.sci.gsfc.nasa.gov/)
   - [Link to SPEI data](https://spei.csic.es/)

5. **Soil Moisture Data**:
   - **Soil Moisture (SM)**: Daily soil moisture data from the European Space Agency (ESA CCI) covering the period from 2000 to 2018. Monthly soil moisture results are calculated using the maximum value synthesis method.
   - [Link to data](http://www.esa-cci.org)

6. **Solar-Induced Chlorophyll Fluorescence Data**:
   - **SIF (Solar-Induced Chlorophyll Fluorescence)**: The GOSIF dataset with high spatiotemporal resolution (0.05°, 8-day and monthly) was developed and covers the maize growing season.
   - [Link to data](http://globalecology.unh.edu)

7. **Other Data**:
   - **Land Use Data**: Obtained from the Resource and Environment Science and Data Center with a spatial resolution of 1 km, including types such as farmland, forest, grassland, water, construction land, and others.
   - **Site-Based Data**: Relative soil moisture data (10 cm) from the China Meteorological Data Network covering 1991 to 2009.
   - **Statistical Data**: Annual data on areas covered by drought, areas affected by drought, and summer maize yield obtained from the National Bureau of Statistics of the People's Republic of China.
   - [Land use data link](http://www.resdc.cn/)
   - [Site-based data link](http://data.cma.cn)
   - [Statistical data link](http://www.stats.gov.cn/)

Due to the varying resolutions and projections of these multi-source remote sensing datasets, the monthly products were spatially resampled onto a 0.00834° (approximately 1 km) geographic Climate Modelling Grid under the World Geodetic System -1984 (WGS-84) geographic coordinate system.

## References

* [Zhang et al., 2021: Establishment of a Comprehensive Drought Monitoring Index.](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9325523)
