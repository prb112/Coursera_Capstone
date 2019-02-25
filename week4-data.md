# Data
To answer the question proposed in the introduction - "What level of risk is there in commuting through the Boston neighborhoods and streets to avoid?".  The analysis uses data sets from the City of Boston open data. This section describes the data sets, the formats, the data quality and the reasons they are included with the analysis. 

The data sets used in the analysis are:

- Vision Zero Crash Records 
    - Crash https://data.boston.gov/dataset/vision-zero-crash-records
    - Fatality https://data.boston.gov/dataset/vision-zero-fatality-records

- Boston Neighborhoods  
    - https://data.boston.gov/dataset/boston-neighborhoods

## Vision Zero Crash Records

The Crash Record dataset records vehicular/bike incidents which result in injury or fatality. The data is comprised of date, time, location and the type of incident. The data is from the start of 2015 to the end of 2018. 

The data is in the CSV format, and contains the following fields: 
- dispatch_ts 	text of the dispatch time
- mode_type 	text 	Type of incident (ped = pedestrian; mv = motor vehicle; bike = bicyclist) [1]
- location_type 	text 	Location "street", "intersection", "other"
- street 	text 	Street Name 	
- xstreet1 	text 	Cross-street 1 	
- xstreet2 	text 	Cross-street 2 	
- x_cord 	text 	X coordinate 	
- y_cord 	text 	Y coordinate 	
- lat 	text 	Latitude 	
- long 	text 	Longitude 	

*Example of the data* 

```
_id	date_time	mode_type	location_type	street	xstreet1	xstreet2	x_cord	y_cord	long	lat
1	2015-01-21 15:07:00	ped	Street	CENTRE ST	RITCHIE ST	LAMARTINE ST	764320.75	2942908.64	-71.1000694606	42.3227879775
2	2015-04-09 16:00:00	ped	Intersection	None	MELNEA CASS BLVD	MASSACHUSETTS AVE	771859.87	2946498.89	-71.0721241969	42.33254716
3	2015-04-25 17:48:00	mv	Street	MASSACHUSETTS AVE	MAGAZINE ST	PROCTOR ST	773210.41	2944795.5	-71.067586742	42.3276600882
```

There are three incident types (Ped = Pedestrian, mv = motor vehicle, bike = cyclist)
```
mv      12489
ped      3131
bike     1741
```

The location type in the neighborhoods are: 
```
Intersection    8698
Street          7238
Other           1425
```

There are 17361 dispatches in total from 2015 to end of 2018. These dispatches involve 7238 streets.

The analysis uses lat, long to position to crash and location_type these features are extracted to identify each incident type and location.  The dispatch_ts is used to split the data into partitioned sets.  The partitioned sets showing a trend over the months of data collection, and the changing environment safety. 

[1] https://data.boston.gov/dataset/vision-zero-crash-records/resource/e4bfe397-6bfc-49c5-9367-c879fac7401d

## Vision Zero Fatality Records
The Crash Fatality dataset records incidents which result in fatality. The data is comprised of date, time, location and the type of incident. The data is from the start of 2015 to the end of 2018. 

The data is in the CSV format, and contains the following fields: 
- dispatch_ts 	text of the dispatch time
- mode_type 	text 	Type of incident (ped = pedestrian; mv = motor vehicle; bike = bicyclist) [1]
- location_type 	text 	Location "street", "intersection", "other"
- street 	text 	Street Name 	
- xstreet1 	text 	Cross-street 1 	
- xstreet2 	text 	Cross-street 2 	
- x_cord 	text 	X coordinate 	
- y_cord 	text 	Y coordinate 	
- lat 	text 	Latitude 	
- long 	text 	Longitude 	

*Example of the data* 

```
_id	date_time	mode_type	location_type	street	xstreet1	xstreet2	x_cord	y_cord	long	lat
2015-01-21 15:07:00,ped,Street,CENTRE ST,RITCHIE ST,LAMARTINE ST,764320.75,2942908.64,-71.1000694606,42.3227879775
2015-04-09 16:00:00,ped,Intersection,,MELNEA CASS BLVD,MASSACHUSETTS AVE,771859.87,2946498.89,-71.0721241969,42.33254716
```

The analysis uses lat, long to position to crash and location_type these features are extracted to identify each incident type and location.  The dispatch_ts is used to split the data into partitioned sets.  The partitioned sets showing a trend over the months of data collection, and the changing environmental safety. 

There are three incident types (Ped = Pedestrian, mv = motor vehicle, bike = cyclist)
```
ped     38
mv      21
bike     6
```

The location type in the neighborhoods are: 
```
Intersection    36
Street          29
```

## Boston Neighborhoods 

The Boston Neighborhoods data set is a GeoJSON definition file which includes neighborhood boundaries and name labels. This data is current as of April 28, 2017

*Example of the Data* showing Chinatown border in Boston
```
{"type":"Feature","properties":{"OBJECTID":33,"Name":"Chinatown","Acres":76.32440999,"Neighborhood_ID":"26","SqMiles":0.12,"ShapeSTArea":3324678.0184608065,"ShapeSTLength":9736.590412617801},"geometry":{"type":"Polygon","coordinates":[[[-71.0579055147603,42.35237863170756],[-71.05810830329557,42.35237200984217],[-71.05840144237023,42.35239732136049]]}
```
From the GeoJSON, the neighborhood name *Name* and the shape of the data are used to enable a visualization of the neighborhoods with the most risk.  The coordinates in the *geometery* of the data establishes a boundary and aggregate within the boundaries the total incidents in each of the neighborhood. The aggregation visualizes the trends within the neighborhoods. 

### Total Neighborhoods

```
from pandas.io.json import json_normalize
df_neighborhood = pd.read_json(file_boston_geo)
df_neighborhood_norm = json_normalize(df_neighborhood['features'])
df_neighborhood_norm['properties.Name'].unique()
```

There are 26 unique neighborhoods in Boston. 
```
array(['Roslindale', 'Jamaica Plain', 'Mission Hill', 'Longwood',
       'Bay Village', 'Leather District', 'Chinatown', 'North End',
       'Roxbury', 'South End', 'Back Bay', 'East Boston', 'Charlestown',
       'West End', 'Beacon Hill', 'Downtown', 'Fenway', 'Brighton',
       'West Roxbury', 'Hyde Park', 'Mattapan', 'Dorchester',
       'South Boston Waterfront', 'South Boston', 'Allston',
       'Harbor Islands'], dtype=object)
```

From this date, the venues array is extracted and counted to indicate the number of FourSquare venues in the location. The venues are indicative of a more public nature using the abscence of the isFuzzed value from the results in order to calculate the degree of commercial venues.   The *lat*/*lng* are used to position the venue within a neighborhood, and the id is used to keep each query unique.  

The data is to be joined on latitude and longitude in order to correlate data and answer the question under study. 

