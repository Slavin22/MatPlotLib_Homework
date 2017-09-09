
# Pyber Ride Sharing

### Analysis

- We see a clear relationship between City Type and each of the other variables: Total Rides, Total Drivers, and Average Fare. As we move from rural to urban, both the number of drivers and number of rides increase. This makes sense as urban cities are generally larger in population than suburban cities, with rural areas being even smaller.


- While those two variables have a direct relationship with city type (specifically size), our last variable has an inverse relationship with city type. The average fare is lowest in urban cities, which tend to have quicker stops within the city.


- Despite the lower average fares in urban areas, these cities still command a majority share of total fares, due to sheer volume of rides.


```python
# Import dependencies
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns
```


```python
# Read in CSVs
ride = pd.read_csv("Resources/ride_data.csv", low_memory=False)
city = pd.read_csv("Resources/city_data.csv", low_memory=False)

# Get fare/ride totals for each city
cityfares = ((ride.groupby("city")["fare"].sum()).to_frame()).reset_index()
cityrides = ((ride.groupby("city")["fare"].count()).to_frame()).reset_index()

# Merge fare/ride totals with city info
cities = pd.merge(city, pd.merge(cityfares, cityrides, on="city"), on="city")
cities.columns.values[3] = "fare_total"
cities.columns.values[4] = "ride_total"
cities.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th>fare_total</th>
      <th>ride_total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>610.58</td>
      <td>28</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Nguyenbury</td>
      <td>8</td>
      <td>Urban</td>
      <td>673.39</td>
      <td>26</td>
    </tr>
    <tr>
      <th>2</th>
      <td>East Douglas</td>
      <td>12</td>
      <td>Urban</td>
      <td>575.72</td>
      <td>22</td>
    </tr>
    <tr>
      <th>3</th>
      <td>West Dawnfurt</td>
      <td>34</td>
      <td>Urban</td>
      <td>647.58</td>
      <td>29</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Rodriguezburgh</td>
      <td>52</td>
      <td>Urban</td>
      <td>490.65</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
</div>



### Bubble Plot of Ride Sharing Data


```python
# Slice city data into city types (Rural, Urban, Suburban)
rural = cities.loc[cities["type"] == "Rural"].reset_index()
rural["average_fare"] = rural["fare_total"] / rural["ride_total"]

urban = cities.loc[cities["type"] == "Urban"].reset_index()
urban["average_fare"] = urban["fare_total"] / urban["ride_total"]

suburban = cities.loc[cities["type"] == "Suburban"].reset_index()
suburban["average_fare"] = suburban["fare_total"] / suburban["ride_total"]
suburban.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th>fare_total</th>
      <th>ride_total</th>
      <th>average_fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>66</td>
      <td>Carrollbury</td>
      <td>4</td>
      <td>Suburban</td>
      <td>366.06</td>
      <td>10</td>
      <td>36.606000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>67</td>
      <td>Williamchester</td>
      <td>26</td>
      <td>Suburban</td>
      <td>377.06</td>
      <td>11</td>
      <td>34.278182</td>
    </tr>
    <tr>
      <th>2</th>
      <td>68</td>
      <td>New Michelleberg</td>
      <td>9</td>
      <td>Suburban</td>
      <td>274.69</td>
      <td>11</td>
      <td>24.971818</td>
    </tr>
    <tr>
      <th>3</th>
      <td>69</td>
      <td>West Evan</td>
      <td>4</td>
      <td>Suburban</td>
      <td>324.16</td>
      <td>12</td>
      <td>27.013333</td>
    </tr>
    <tr>
      <th>4</th>
      <td>70</td>
      <td>South Gracechester</td>
      <td>19</td>
      <td>Suburban</td>
      <td>595.57</td>
      <td>19</td>
      <td>31.345789</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Slice above DataFrames into Series
rural_cities = rural.iloc[:,1]
rural_rides = rural.iloc[:,5]
rural_drivers = rural.iloc[:,2]
rural_fares = rural.iloc[:,6]

urban_cities = urban.iloc[:,1]
urban_rides = urban.iloc[:,5]
urban_drivers = urban.iloc[:,2]
urban_fares = urban.iloc[:,6]

suburban_cities = suburban.iloc[:,1]
suburban_rides = suburban.iloc[:,5]
suburban_drivers = suburban.iloc[:,2]
suburban_fares = suburban.iloc[:,6]
```


```python
# Initialize plot + format the scatters
fig, ax = plt.subplots()
ax.scatter(urban_rides, urban_fares, urban_drivers*5, alpha=0.75, facecolor="lightcoral", edgecolors="black", linewidths=1.5, label="Urban")
ax.scatter(suburban_rides, suburban_fares, suburban_drivers*5, alpha=0.75, facecolor="lightskyblue", edgecolors="black", linewidths=1.5, label="Suburban")
ax.scatter(rural_rides, rural_fares, rural_drivers*5, alpha=0.75, facecolor="gold", edgecolors="black", linewidths=1.5, label="Rural")

# Format and print the full plot

plt.legend(title="City Types")
plt.title("Pyber Ride Sharing Data (2016)")
plt.xlabel("Total Number of Rides (Per City)")
plt.ylabel("Average Fare ($)")
plt.annotate("Note:\nCircle size correlates with driver count per city", xy=(0,20), xytext=(42,42))

plt.xlim(0,40)
plt.ylim(15,55)
plt.show()

# Need to format axes
```


![png](output_6_0.png)


### Total Fares by City Type


```python
# Sum total fares by city type
cities = cities.reset_index(drop=True)
citytype_fares = cities.groupby("type")["fare_total"].sum()
labels = ["Rural", "Suburban", "Urban"]
explode = (0, 0, 0.05)

# Format + show plot
plt.title("% of Total Fares by City Type")
plt.pie(citytype_fares, explode=explode, labels=labels, autopct='%1.1f%%', shadow=True, colors=["gold", "lightskyblue", "lightcoral"], startangle=135)
plt.show()
```


![png](output_8_0.png)


### Total Rides by City Type


```python
# Sum total rides by city type
citytype_rides = cities.groupby("type")["ride_total"].sum()

# Format + show plot
plt.title("% of Total Rides by City Type")
plt.pie(citytype_rides, explode=explode, labels=labels, autopct='%1.1f%%', shadow=True, colors=["gold", "lightskyblue", "lightcoral"], startangle=135)
plt.show()
```


![png](output_10_0.png)


### Total Drivers by City Type


```python
# Sum total drivers by city type
citytype_drivers = cities.groupby("type")["driver_count"].sum()

# Format + show plot
plt.title("% of Total Drivers by City Type")
plt.pie(citytype_drivers, explode=explode, labels=labels, autopct='%1.1f%%', shadow=True, colors=["gold", "lightskyblue", "lightcoral"], startangle=135)
plt.show()
```


![png](output_12_0.png)

