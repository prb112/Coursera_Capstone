# Data
To answer the question proposed in the introduction - "What level of risk is there in commuting through the Boston neighborhoods and how commercial are the neighborhoods?".  The analysis uses data sets from the City of Boston and the FourSquare APIs. This section describes the data sets, the formats and the reasons they are included with the analysis. 

The data sets used in the analysis are:

- Vision Zero Crash Records 
    - https://data.boston.gov/dataset/vision-zero-crash-records
    - https://data.boston.gov/dataset/vision-zero-fatality-records

- Boston Neighborhoods  
    - https://data.boston.gov/dataset/boston-neighborhoods

- FourSquare API Search 
    - https://developer.foursquare.com/docs/api/venues/search

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

The analysis uses lat, long to position to crash and location_type these features are extracted to identify each incident type and location.  The dispatch_ts is used to split the data into partitioned sets.  The partitioned sets showing a trend over the months of data collection, and the changing environmental safety. 

[1] https://data.boston.gov/dataset/vision-zero-crash-records/resource/e4bfe397-6bfc-49c5-9367-c879fac7401d

## Boston Neighborhoods 

The Boston Neighborhoods data set is a GeoJSON definition file which includes neighborhood boundaries and name labels. This data is current as of April 28, 2017

*Example of the Data* showing Chinatown border in Boston
```
{"type":"Feature","properties":{"OBJECTID":33,"Name":"Chinatown","Acres":76.32440999,"Neighborhood_ID":"26","SqMiles":0.12,"ShapeSTArea":3324678.0184608065,"ShapeSTLength":9736.590412617801},"geometry":{"type":"Polygon","coordinates":[[[-71.0579055147603,42.35237863170756],[-71.05810830329557,42.35237200984217],[-71.05840144237023,42.35239732136049]]}
```
From the GeoJSON, the neighborhood name *Name* and the shape of the data are used to enable a visualization of the neighborhoods with the most risk.  The coordinates in the *geometery* of the data establishes a boundary and aggregate within the boundaries the total incidents in each of the neighborhood. The aggregation visualizes the trends within the neighborhoods. 

## FourSquare API Search 

The FourSquare API Search lists the venues near the long/lat enabling the analysis of how commerical the neighborhood is.  THe API call will be limited to venues within a radius of 1000 meters and within an accurancy of 10 meters.  The commercial designation is done using a total number of responses for the latitude and longitude on the page relative to all of the other queries.  The commercial designation is relative. 

*Example of the Data*
```
{
  "meta": {
    "code": 200,
    "requestId": "5ac51d7e6a607143d811cecb"
  },
  "response": {
    "venues": [
      {
        "id": "5642aef9498e51025cf4a7a5",
        "name": "Mr. Purple",
        "location": {
          "address": "180 Orchard St",
          "crossStreet": "btwn Houston & Stanton St",
          "lat": 40.72173744277209,
          "lng": -73.98800687282996,
          "isFuzzed": true,
          "labeledLatLngs": [
            {
              "label": "display",
              "lat": 40.72173744277209,
              "lng": -73.98800687282996
            }
          ]
        }
    ]
}
```

From this date, the venues array is extracted and counted to indicate the number of FourSquare venues in the location. The venues are indicative of a more public nature using the abscence of the isFuzzed value from the results in order to calculate the degree of commercial venues.   The *lat*/*lng* are used to position the venue within a neighborhood, and the id is used to keep each query unique.  

The data is to be joined on latitude and longitude in order to correlate data and answer the question under study. 

