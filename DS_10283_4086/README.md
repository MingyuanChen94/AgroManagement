# Producing continuous weekly LAI time-series by fusing earth observation data

## Description 

This is a python package that uses data from the Sentinel-2 (multispectral images) and the Sentinel-1 (synthetic apperture radar backscatter) satellite systems, along with data on vapour pressure deficit (VPD), in order to produce field-scale, continuous, weekly estimates of Leaf Area Index (LAI). Spatial data on climate variables, Sentinel-1 granules and Sentinel-2 images corresponding to the field and time-period of interest are provided to the algorithm. The algorithm has been tested for managed grassland fields in the United Kingdom (UK). Its use for ecosystems other than grasslands and croplands is not recommended.  


## Methodology 

The algorithm splits every examined field into 25 bounding boxes (subfields). For each subfield and examined day the algorithm estimates the corresponding subfield-wide mean value for : (1) S1-VV/VH (10m, 2-3 day intervals), (2) S2-LAI (20-40m, irregular frequency due to cloudiness) and (3) VPD (5km, daily) . The algorithm uses 80% of the per-subfield dataset (all bounding boxes and examined dates) to train a Random Forest (RF) model that predicts LAI by using S1 VV, S1 VH, VPD and Day-of-Year as predictors. 20% of all the per-subfield data are used to validate the RF-predicted LAI (i.e. estimate R2 - coefficient of determination). Thereafter, the RF model is used to fill the gaps in the S2-based LAI weekly times-series. Each data point the final LAI time-series presents the field-mean LAI on the 1st day of every week of the examined period. The number of bounding boxes (subfields) and RF training/validation ratio (80%/20%) are hard-coded and they should be edited before package installation if necessary.

## Requirements 

1. Location of a field provided as a .geojson (http://geojson.io)
2. Sentinel-2 images/tiles - atmospherically corrected i.e. "L2A" product  (https://registry.opendata.aws/sentinel-2/)
3. Sentinel-1 granules (https://search.asf.alaska.edu/#/)
4. ERA-5 climate data from ECMWF (https://www.ecmwf.int/en/forecasts/datasets/reanalysis-datasets/era5)
5. ESA's SNAP application installed (https://step.esa.int/main/download/snap-download/)
6. Python (>=3.8) and GDAL installed 

## Package installation 

Download this folder. Unzip it, if necessary. Navigate to the downloaded/unzipped folder and run "python setup.py install"

## ECMWF-ERA5 data 

Time-series of vapour pressure deficit (VPD) are required to implement the algorithm. VPD time-series for a given area/polygon can be obtained from ECMWF (see : https://confluence.ecmwf.int/display/WEBAPI/Access+ECMWF+Public+Datasets). The .py script provided below downloads hourly data on temperature and dewpoint (5km resolution) for 2015 for an area that contains the UK. '2m_temperature', '2m_dewpoint_temperature' are the climate variables needed for the calculation of VPD. The script saves the downloaded data as ERA5_2015.nc. Please use the script below as a template and only edit the year, area limits and file name ending (i.e. ERA5_YYYY.nc)

import cdsapi

c = cdsapi.Client()

c.retrieve(
 'reanalysis-era5-land',
 {
     'format': 'netcdf',
     'variable': [
         '2m_temperature', '2m_dewpoint_temperature',
     ],
     'year': [
         '2015', 
     ],
     'month': [
         '01', '02', '03',
         '04', '05', '06',
         '07', '08', '09',
         '10', '11', '12',
     ],
     'day': [
         '01', '02', '03',
         '04', '05', '06',
         '07', '08', '09',
         '10', '11', '12',
         '13', '14', '15',
         '16', '17', '18',
         '19', '20', '21',
         '22', '23', '24',
         '25', '26', '27',
         '28', '29', '30',
         '31',
     ],
     'time': [
         '00:00', '01:00', '02:00',
         '03:00', '04:00', '05:00',
         '06:00', '07:00', '08:00',
         '09:00', '10:00', '11:00',
         '12:00', '13:00', '14:00',
         '15:00', '16:00', '17:00',
         '18:00', '19:00', '20:00',
         '21:00', '22:00', '23:00',
     ],
     'area': [
         58.81, -11.66, 50,
         3,
     ],
 },
 'ERA5_2015.nc')
 
## Licence 

Please read LICENCE document for information

