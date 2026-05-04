# isa401b_disaster_housing


<br>


Miami University ISA401B Final Project, Data Organization

**Your tool for analyzing the impact of natural disasters on property values**


<br>

## Necessary Packages

The following packages are required to sensure successful execution of the code:

| Package | Description |
|-------|-------------|
| `tidyverse` | Includes dplyr, tidyr, readr |
| `httr` | For API calls |
| `jsonlite` | For parsing JSON from FEMA |
| `lubridate` | For working with dates |


<br>


## What is this Code?

This Rmd compiles, cleans, and joins, validates, and saves data from 3 datasets, 
allowing you to properly analyze, visualize, and display the aforementioned information.


## Dataset Information, 1

**Dataset:** OpenFEMA Dataset: Disaster Declarations Summaries - v2

**Publisher:** FEMA

**Website:** https://www.fema.gov/openfema-data-page/disaster-declarations-summaries-v2

The OpenFEMA Dataset is a summarized dataset describing all federally declared disasters, beginning in 1953. This information is available for the nation as a whole, for individual states, and for county areas.


### Key Fields

| Field | Description |
|-------|-------------|
| `disasterNumber` | Sequentially assigned number used to designate an event or incident declared as a disaster. |
| `state` | The name or phrase describing the U.S. state, district, or territory. |
| `declarationDate` | Date the disaster was declared. |
| `incidentType` | The primary or official type of incident such as fire or flood. |
| `incidentBeginDate` | Date the incident itself began. |
| `incidentEndDate` | Date the incident itself ended. |
| `fipsStateCode` | FIPS three-digit numeric code used to identify counties and county equivalents in the United States, the District of Columbia, US territories, outlying areas of the US and freely associated states. |
| `fipsCountyCode` | FIPS two-digit numeric code used to identify the United States, the District of Columbia, US territories, outlying areas of the US and freely associated states. |


<br>


## Dataset Information, 2

**Dataset:** Zillow Home Value Index (ZHVI)

**Publisher:** Zillow

**Website:** https://www.zillow.com/research/data/

The Zillow Home Value Index is a measure of the typical home value and market changes across a given region and housing type. It reflects the typical value for homes in the 35th to 65th percentile range. Available as a smoothed, seasonally adjusted measure and as a raw measure.

### Key Fields

| Field | Description |
|-------|-------------|
| `RegionID` | Concatenation of State FIPS code and County FIPS code. |
| `State` | The name or phrase describing the U.S. state, district, or territory. |
| `CountyName` |  The name or phrase describing the U.S. county, parish, or borough. |
| `date` | The date of the observation. |
| `zhvi` | The typical value of homes in the 35th-65th percentile range. |

NOTE: In the original file, date and property values were displayed horizontally, giving us a wide data table. The date and observation value were pivoted in Tableau for proper analysis.


<br>


## Dataset Information, 3

**Dataset:** Losses from Natural Disasters

**Publisher:** Federal Reserve Bank of New York

**Website:** https://www.newyorkfed.org/research/policy/natural-disaster-losses/#interactive

Displays county-level estimates of inflation-adjusted annual direct damages from natural disasters across the United States, based on a methodology that was developed by New York Fed economists and applied to the National Oceanic and Atmospheric Administration$'$s Storm Events Database.

### Key Fields

| Field | Description |
|-------|-------------|
| `event_id` | A code assigned to each event/disaster by NOAA. |
| `fips` | State-county FIPS code. |
| `disaster_group` | Grouping of event types. |
| `damages_property` | Monetized losses of the physical destruction of property from the event. The variable is expressed in nominal dollars (i.e., not inflation adjusted). |
| `fatalities_direct` |  Fatalities that are caused by impact or debris from the event itself. |

NOTE: By running the R code, this data is scraped as a .zip file, downloaded, unzipped, and read, before cleaning.

<br>
 
 
## Cleaning Process

### Zillow:
  1. Fields RegionID, SizeRank, RegionType, StateName, and Metro are dropped.
  2. county_fips is determined through mutation of StateCodeFIPS and MunicipalCodeFIPS.
  3. Date and ZHVI were pivoted, turning wide data into long data. 

Each row now displays one county, one month, and one median home value


<br>


### FEMA:
  1. county_fips is determined through mutation of StateCodeFIPS and MunicipalCodeFIPS.
  2. Fields county_fips, state, disasterNumber, incidentType, declarationDate, incidentBeginDate, incidentEndDate are selected.
  3. state is filtered to only include States


<br>


### Federal Reserve Bank of New York:
  1. county_fips is determined through mutation of fips.
  2. Fields county_fips, begin_date, disaster_group, damages_property, and fatalities_direct are selected


<br>


## Data Merging
 1. FEMA data is filtered to only include observations after the year 2000 (the earliest date of observations provided by Zillow).
 2. FEMA and NYFED data is merged through a LEFTJOIN, based on county_fips and date, using a many-to-many relationship.
 3. The Zillow dataset is merged with the FEMA/NYFED dataset, again based on county_fips and date, using a many-to-many relationship.


<br>


## Data Validation
 1. A data validation tibble is created, ensuring that data is not duplicated, missing, formatted incorrectly, or failed to match during the JOIN process.
 2. Violations of aforementioned rules are recorded, displayed, and removed.
 

<br>


## Saving Final Dataset
 1. write_csv is used to save the cleaned, merged, and organized dataset to a new file, ready for import into Tableau.
 2. Final dataset dimensions, date ranges, unique county counts, and counties with at least one disaster are recorded and printed, providing a summary of the dataset.

 