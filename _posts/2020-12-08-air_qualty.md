---
layout: post
title:  "Air Quality Forecasts"
date:   2020-12-08
categories: earth observation, health, air quality
---
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    processEscapes: true
  }
});
</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

During a pandemic the air quality might not be seen as the most pressing issue but, according to the World Health Organization ([WHO](https://www.who.int/health-topics/air-pollution#tab=tab_1)), air pollution kills an estimated 7 million people world wide every year. If we wonder what might be the situation in Europe, the European Environment Agency ([EEA](https://www.eea.europa.eu/themes/air/health-impacts-of-air-pollution)) would inform us that in 2018 nearly 379000 premature deaths were attributable to particulate matter (PM2.5) and 54000 to NO2. The WHO has established some [guidelines](https://www.who.int/news-room/fact-sheets/detail/ambient-(outdoor)-air-quality-and-health) on the quantity of certain pollutants above which an impact on the health of the population is expected. For example PM2.5 should not exceed 10 $$\mu g/m^3$$ and NO2 should not exceed an annual mean value of 40 $$\mu g/m^3$$ or a 1-hour mean of 200 $$\mu g/m^3$$. Given the relevance of these pollutants for the population's health, every country and region in Europe has built a network of ground stations to monitor them, like those managed by the Agenzia Regionale Protezione Ambientale del Lazio ([ARPA](https://qa.arpalazio.net/)) for the Latium region in Italy. The data from the ground stations in Europe are collected by the EEA and assimilated by the European Centre for Medium-Range Weather Forecasts (ECMWF) and other eight institutions across Europe into their air quality models for analysis and forecasts. The numerical models are able to provide an estimate of the concentration of the pollutants in areas that are far from the ground stations with a spatial resolution of 10 km. The ECMWF provides through its Copernicus Atmosphere Monitoring Service (CAMS) the analysis and forecasts about the air quality as the median of the [ensemble](https://confluence.ecmwf.int/display/CKB/CAMS+Regional%3A+European+air+quality+analysis+and+forecast+data+documentation) of the models. The data about gas traces such as NOx, Ozone and SO2 are now available also from satellites, e.g. Sentinel 5-p through its sensor [TROPOMI](http://www.tropomi.eu/) and MetOp through [GOME-2](https://atmos.eoc.dlr.de/app/missions/gome2) but in this post I want to show how the model forecasts from the CAMS service can be fetched by everyone and visualized in an animation on a web page.    

## The Copernicus Atmosphere Monitoring Service
The Copernicus Atmosphere Monitoring Service ([CAMS](https://atmosphere.copernicus.eu/)) provides information related to air quality, atmospheric composition, greenhouse gases, solar irradiance. The datasets released by the CAMS service is the result of assimilation processes in which observations from satellites and ground stations are used to update and correct every hour the estimation computed by a numerical model of the atmosphere. The CAMS is operated by the European Centre for Medium-Range Weather Forecasts ([ECMWF](https://www.ecmwf.int/)) on behalf of the European Commission.

### CAMS European air quality forecasts
The CAMS provides its datasets as open data, available to all for free, through a web page and a web service. For the air quality forecasts a user can select, among other options

- the variables, that is the physical parameters she is interested in
- the model she wants to use (nine models are available, plus the ensemble)
- the levels, or heights for which she wants the forecasts
- the area of interest, delimited by north and south latitudes and west and east longitudes
- the date from which the forecast should start
- the lead time hours, that is the hours of the forecasts
- the format of the data (CSV, NetCDF)

If you want to set up a service based on the data provided by the CAMS you will likely use the web service API. In order to use the API, you have to be registered into the CADS and

1. login
2. copy your ADS API key in the .condarc file in your home folder
3. install the cdsapi Python package

For more information on how to register to the CAMS follow the [how-to instructions](https://ads.atmosphere.copernicus.eu/api-how-to). The NetCDF format is easy to use in Python with the xarray library. If you choose one single day as the start of the forecasts the file returned by the service will contain the forecasts in a multidimensional array. The name of the dimensions (metadata) are standardized and a description is provided in the file. We can easily visualize the data for one forecasts using Matplotlib

![Forecast example](/assets/no2_forecast_example.png)

If we have requested the forecasts at different lead time hours we might want to visualize each forecast as a frame in an animation. The details on how to create an animation with Matplotlib are a little bit more convoluted but a Jupyter notebook is available on my Github repository with an [example of an animation](https://nbviewer.jupyter.org/github/luigiselmi/datascience/blob/master/python/copernicus/air_quality_forecasts.ipynb?flush_cache=true) build on a sequence of hourly forecasts for 4 consecutive days. The Python code can be used to fetch and visualize other variables with a little work and a different selection of the request's parameters.  
