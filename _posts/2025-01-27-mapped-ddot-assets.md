# An Examination into Detroit Department of Transportation’s Bus Stop Assets
## Few places to sit and limited near term improvement awaits
As I’ve been a long-time public transportation advocate in the City of Detroit, myself and others have long advocated for service improvements like seating and shelters. Basic amenities that make using our transportation system more accessible for all of us. I remember sitting in [monthly community input meetings](https://detroitmi.gov/departments/detroit-department-transportation) with the former DDOT director, Mikel Oglesby, and hearing that the transit agency would like to invest in seating of some kind to many more stops throughout their network. 

There were discussions about something like a [simmie seat](https://simmeseat.com/) being utilized to quickly scale the seating availability throughout the network. An affordable solution to improve accessibility, especially for our aging population that often can’t wait standing for the extended periods of time we often face when riding the bus in Detroit. 

After Mr. Oglesby’s departure in 2023, I had not heard much about an investment into the bus stop infrastructure until I joined Transit Riders United for their Transit Talk Tuesday which hosted DDOT on October 1st, 2024. In that meeting ([slides for the interested](/assets/DDOT-TRU-TRANSIT-TALK-TUESDAY.pdf)), we saw that the city was continuing with plans to replace old and install new bus shelters throughout the city. What grabbed my attention wasn’t that they wanted to get to nearly 300 shelters in the network by the end of 2027 but that only 4-5% of stops had shelters. 

This was on my mind lately and since I found [this dataset](https://services2.arcgis.com/qvkbeam7Wirps6zC/ArcGIS/rest/services/DDOT_Bus_Stops_202409/FeatureServer) from the city’s ArcGIS REST Services Directory, I wanted to take a closer look at what is publicly available from DDOT. This dataset is an account of all DDOT bus stops (and their assets) as of September, 2024. I did, however, have to clean up the dataset a bit as when I reviewed for any odd or missing data, I found that there were 34 bus stop instances where there was no location or route information. 

Afterwards, we’re left with 5091 bus stops (4 more than DDOT said they have in October’s meeting). Now, we can take a deeper dive into the data. My interest was in the assets at each bus stop. Does it have a trash can, a bench, or a shelter? 

### Assets at DDOT Stops:
- Total Stops: 5,091
- Stops with Assets: 964 (~19%)
  - Benches: 77 (1.51%)
  - Shelters: 228 (4.48%)
  - Trash Cans: 868 (17.05%)

When you include the benches, there is **seating at *just 5.26%* of stops** throughout their network.
  > <sub>Note, this is less than the combined total of seating and shelters due to some stops being labelled as having a bench and a shelter</sub> 

Their plan is to reach 299 shelters in total over the next few years with the last being installed in 2027. Which will increase the seating percentage to 7.3% of all stops. Just 375 out of more than five thousand will have seating. 

Limited seating in the network aside, the other thing I thought was fascinating was just how much of these assets were under the responsibility of a third party that was not DDOT nor the City of Detroit. About 55% of all benches, 20% of shelters, and 36% of trash cans were under a third party’s purview. With this in mind, I wanted to map out some of these to see if there were any obvious gaps or interesting insights to be gained.

To do this, I needed to transform the latitude and longitude data points from my Pandas dataframe into something that could be accessible to the Python package, GeoPandas. To visualize, I used Folium so I could craft interactive maps that let you see where assets are and who was responsible for them. 

[![Map showing DDOT bus routes, bus stop assets, and city neighborhoods ](/images/map-of-city-ddot-seating-routes-neighborhoods.PNG)](https://tedtansley.github.io/assets/detroit_transit_asset_map_nhoods_routes.html)
 <sub>To make it easier, I made all city owned (City of Detroit & DDOT) a green color and all third party owned a yellow color. For assets that had two stated responsibility holders, I made a red. </sub>

### Some of the immediate insights I could gleam were:
- Nearly all of Jefferson Avenue has trash cans responsible to a third party. Which I know to be [Jefferson East Inc.](https://www.jeffersoneast.org/clean-safe#:~:text=MAINTAINING%20A%20CLEAN%20CORRIDOR) 
- All of downtown’s trash cans have a third party responsible for them. 
- In Hamtramck, Third parties have responsibility for all assets with the exception of three trash cans along Conant Street.
- With the exception of a few along the northern part of Chene Street, the lower Eastside does not have benches. 
- Southwest Detroit neighborhoods don't have seating nor shelters.

I could list out all the neighborhoods that are being underserved, but you can also take a look at the map and see the neighborhood boundaries and transit lines. If you find your neighborhood among those without any seating, I would urge you to take the [quick action from Transit Riders United](https://www.detroittransit.org/double-ddot/) and email your city officials to double the investment into DDOT. 

#### [My Code](https://colab.research.google.com/drive/1ve4LUd3RIyxspHDOn8jX8jpDfCWh7YBd?usp=sharing)
