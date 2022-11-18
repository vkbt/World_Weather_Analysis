# Using OpenWeather and Google API to Identify Travel Destinations, Nearby Hotels and Create an Interactive Travel Map.

## Overview
Jack, the head of analysis for the user interface team of PLANMYTRIP, a top travel technology company that specializes in internet-related services in the hotel and lodging industry, asked us to create a code that collects customers preferred travel criteria and creates an interactive itinerary map with destination cities, travel routes and a marker layer map.


## Resources
* Data Source: [WeatherPy_Database.csv](https://github.com/vkbt/World_Weather_Analysis/blob/main/Weather_Database/WeatherPy_Database.csv), [WeatherPy_vacation.csv](https://github.com/vkbt/World_Weather_Analysis/blob/main/Vacation_Search/WeatherPy_vacation.csv), 
* Software and packages: Anaconda **2022.05**, Python **3.7.13**, Conda **22.9.0**, Jupyter Notebook **6.4.12**, Pandas **1.3.5**,  Citipy **0.0.5**, Numpy **3.7**, Gmaps **0.9.0**, Requests **2.28.1**
* API: [OpenWeather API](https://openweathermap.org/api), [Google API](https://developers.google.com/maps) (Directions, Geocoding, Maps JavaScrip, Places)



## Summary

We split this project into three phases:
1. Retrieve Weather Data
2. Create a Customer Travel Destinations Map
3. Create a Travel Itinerary Map


**1. Retrieve Weather Data**

Using **Conda** package management system we created a new virtual environment that runs the latest version of **Python 3.7** and contains all the **Anaconda** packages including **Jupyter Notebook** and named it PythonData. **Jupyter Notebook** is a powerful interactive computational environment that supports Python and is largely used for data analysis and visualization. We used **NumPy**, a Python library, that offers comprehensive mathematical functions, random number generators, linear algebra routines, Fourier transforms, and more. We also used **Pandas**, a fast and powerful software library built on top of Python, to manipulate, analyze and visualize the data. Finally, we used [**Citipy**](https://pypi.org/project/citipy/#description) - a library for city names with geo-coordinates.

We started with the [**np.random.uniform**](https://numpy.org/doc/stable/reference/random/generated/numpy.random.uniform.html) function and generated a set of 2,000 random latitudes and 2,000 random longitudes. We created [**tuples**](https://docs.python.org/3/c-api/tuple.html) containing latitude and longitude combinations using Python [**zip**](https://docs.python.org/3.3/library/functions.html#zip) function. Using CitiPy module we iterated through latitudes and longitudes, retrieved the nearest city and stored city names in a list. Since approximately 70% of the Earth's surface is covered by water, roughly 70% of the latitude and longitude coordinates we generated were positioned over water, the remaining 30% of latitudes and longitudes produced a result of 784 cities.

We imported additional libraries: [**Requests**](https://requests.readthedocs.io/en/latest/) (a Python’s HTTP library), [**Time**](https://docs.python.org/3/library/time.html) (provides many ways of representing time in code, such as objects, numbers, and strings), [**Datetime**](https://docs.python.org/3.7/library/datetime.html)(module that allows us to work with date and time objects).

We signed up for OpenWeather API key, imported it and and assembled the OpenWeatherMap’s API [URL call](https://openweathermap.org/current) as a string variable. The OpenWeatherMap API only allows 60 calls for per minute on their free tier, so we broke our list of cities in sets of 50. We created a for loop with Python [**enumerate**](https://docs.python.org/3/library/functions.html#enumerate) function, referenced the index and the city in the list and added the conditional statement checking if the remainder of the index divided by 50 was equal to 0 and if the index was greater than or equal to 50. If the statement was true, then the set_count and the record_count were incremented by 1 and [**time.sleep(60)**](https://docs.python.org/3/library/time.html) command was called to pause for 60 seconds between requests. Inside the conditional statement, we created the URL endpoint for each city. We added a print statement that tells us the record count and set count, and the city that is being processed. We ran API request for each city and parsed [**JSON**](https://www.json.org/json-en.html) file for current city, latitude, longitude, maximum temp, humidity, cloudiness, wind, country, and weather description using [**requests.get**](https://requests.readthedocs.io/en/latest/) function. We also added [**try-except**](https://docs.python.org/3.7/tutorial/errors.html) block to our code in case there was no weather data for the city and added the **pass** statement to allow the code to continue. We created a new data frame containing the weather data and saved it as [WeatherPy_Database.csv file](https://github.com/vkbt/World_Weather_Analysis/blob/main/Weather_Database/WeatherPy_Database.csv).

**2. Create a Customer Travel Destinations Map**

We imported **Pandas**, **Requests**, **Gmaps**, and **Google API key** into a new **Jupyter Notebook** file. We read the data from previously created [WeatherPy_Database.csv](https://github.com/vkbt/World_Weather_Analysis/blob/main/Weather_Database/WeatherPy_Database.csv) file using [**pandas.read_csv**](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html) function. We wrote two input statements that prompt users to enter minimum and maximum temperature criteria for their vacation. We used these user inputs to narrow the list via [**pandas.DataFrame.loc**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.loc.html) function. We dropped rows with missing values using  [**pandas.DataFrame.dropna**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.dropna.html) function and used [**pandas.DataFrame.copy**](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.copy.html) function to create a data frame with new column order as well as added a new empty column “Hotel Names” and set its values to None.

We created a for loop to iterate through  our new data frame to get latitude and longitude of each city and called Google API to retrieve [**JSON**](https://www.json.org/json-en.html) data with hotel information for each city using [**requests.get**](https://requests.readthedocs.io/en/latest/) function. We then wrote hotel names into a newly created “Hotel Names" column. Additionally, we wrapped our code into a [**try: and except:**](https://docs.python.org/3/tutorial/errors.html) block to make sure the code skips missing hotels and continues to run. We dropped the rows in the data frame where there was no hotels found, stored the results into a new data frame and saved the data as [WeatherPy_Vacation.csv](https://github.com/vkbt/World_Weather_Analysis/blob/main/Vacation_Search/WeatherPy_vacation.csv) file.

To add a pop-up box for each city that displays the hotel name, city and temperature, we created an info box template (a variable that is assigned to a multiline string using three quotes) and added our values to the formatting template. We looped through data frame using [**pandas.DataFrame.iterrows**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html) function to  get these values from each row, added them to the formatting template and stored the data in a list. We created a new data frame containing latitude and longitude from each row. Finally we created a [**google map with a marker layer map and pop-up markers**](https://jupyter-gmaps.readthedocs.io/en/latest/tutorial.html#markers-and-symbols), using a data frame with latitudes and longitudes as locations and a list with values for each marker’s pop-up box.

<img src="https://github.com/vkbt/World_Weather_Analysis/blob/main/Vacation_Search/WeatherPy_vacation_map.png" width=100% height=100%>

**3. Create a Travel Itinerary Map**

We imported **Pandas**, **Requests**, **Gmaps**, and **Google API key** into a new **Jupyter Notebook** file. We read the data from previously created csv file using [**pandas.read_csv function**](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html).

We reused previous code to produce the marker layer map with a pop-up markers for each city that we created in Step 2.

We picked four random cities and created individual data frames (start, end and each stop) for a round trip with three stops using [**pd.DataFrame.loc**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.loc.html) function and retrieved latitude-longitude pair as tuples from each data frame using [**pandas.DataFrame.to_numpy**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_numpy.html) function and list indexing. Finally, we created a directions layer map using the start and end latitude-longitude pairs, three stops as the waypoints and specified travel mode as driving.

<img src="https://github.com/vkbt/World_Weather_Analysis/blob/main/Vacation_Itinerary/WeatherPy_travel_map.png" width=100% height=100%>

We also refactored our code for marker layer map of the cities on the travel route. We used [**pandas.DataFrame.concat**](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.concat.html) function code to combine  individual city data frames into one data frame, created and added values to the list for pop-up box markers, looped through data frame using [**pandas.DataFrame.iterrows**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html) function and  stored the data in a list. We created a new data frame containing latitude and longitude from each row. And at last we created a final marker layer map with pop-ups for cities on the travel route.

<img src="https://github.com/vkbt/World_Weather_Analysis/blob/main/Vacation_Itinerary/WeatherPy_travel_map_markers.png" width=100% height=100%>

## Challenge Overview

For this project we collected and analyzed data, using Pandas, citipy and numpy libraries. We leveraged different APIs, translated and parsed multiple JSON files and formats as well as utilized try and except blocks as error catching mechanisms. We exported the data and cleaned it. We enhanced the code by adding input statements for users to filter the data for their weather preferences and added the weather description to the weather data in info boxes.  In the end we created marker layer maps with info boxes and marker layer maps with travel directions. 
