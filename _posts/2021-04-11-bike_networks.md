---
title: 'Visualizing and Analyzing Bicycle Infrastructure using OSMnx'
date: 2021-05-10
permalink: /posts/2021/4/bike_networks/
tags:
  - python
  - data visualization
  - OSMnx
---

I've primarily commuted by bicycle for over 6 years. After moving to Chicago for graduate school, I quickly came to appreciate the dedicated lakefront bike trail. Bike trails and bike lanes make commuting much more enjoyable, and more importantly, much safer. However, some cities do a better job than others at building this infrastructure. The goal of this project is to visualize cycleway networks and also find a way to quantify the best and worst bike cities in the US. 

![](https://williamthyer.github.io/images/bike_networks/best_worst_cities.png)

As a quick side note, I'm not a urban planning researcher or spatial scientist! I'm getting my PhD in Psychology right now. But as a cyclist and data scientist, this seemed like a really interesting problem to tackle in Python. For information from a more reputable source, checkout [People for Bikes](https://www.peopleforbikes.org/) who do some interesting work on ranking cities' bikeability. 

## Creating the Cycleway Map

To get started, I had to find a way to interface with [OpenStreetMap](www.openstreetmap.org), which is basically an open-source Google Maps with specific cycleway information. After some searching, I found the amazing [OSMnx](https://osmnx.readthedocs.io/en/stable/) library by [Geoff Boeing](https://geoffboeing.com/), a professor of Urban Planning at USC. This library allowed me to query the OSM, and also includes network analysis tools. 

As an example of how easy it is to access OSM data, here's a snippet of code that produces the network of all public roads in the city of Chicago, Illinois:
```Python
import osmnx as ox
roads = ox.graph_from_place('Chicago,IL',network_type='drive')
ox.plot_graph(
	roads,
	node_size=0,
	edge_linewidth=.25,
	edge_color='dimgrey')
```
#insert roads image here

When I first saw this map, it really hit me how powerful this database was, and how user-friendly OSMnx is. The next challenge was to do the same thing but for bike infrastructure. It's a bit more complicated, because I want dedicated bike trails as well as bike lanes (collectively referred to as cycleways). This [Github thread](https://github.com/gboeing/osmnx/issues/151) basically gave me the answer:

```Python
# Configuring osmnx
useful_tags = ox.settings.useful_tags_way + ['cycleway']
ox.config(use_cache=True, log_console=True, useful_tags_way=useful_tags)
# Querying for roads and bike trails
cycleways = ox.graph_from_place(query = city_name, network_type='bike', simplify=False)
# Finding all non-cycleways in the network
non_cycleways = [(u, v, k) for  u, v, k, d  in  cycleways.edges(keys=True, data=True) if  not ('cycleway'  in  d  or  d['highway']=='cycleway')]
# Remove non-cycleways and isolated nodes
cycleways.remove_edges_from(non_cycleways)
cycleways = ox.utils_graph.remove_isolated_nodes(cycleways)
```
That was pretty much the most complicated code in this project, and I didn't even have to write it! I stuck that code in a function called `get_cycleways()`. Here's what the cycleways look like plotted, including a footprint of the whole city:

```Python
# Get footprint of entire city
city_area = ox.geocode_to_gdf(city_name)
# Get cycleways network
cycleways = get_cycleways(city_name)

# Plotting
fig, ax = plt.subplots()
city_area.plot(ax=ax, facecolor='gainsboro')
ox.plot_graph(
	cycleways,
	ax=ax,
	node_size=0,
	edge_linewidth=.85,
	edge_color='limegreen')
```
 #insert cycleways map

Awesome! Next I put all of the query code into one function called `get_city`. I also made a function called `plot_cycleways` that handles all of the plotting. Here's what that looks like in action:
```Python
# load city info and calculate road to cycleway ratio
city_name =  'Chicago, IL'
cycleways,roads,city_area = get_city(city_name)

# create the map
fig,ax = plot_cycleways(
	city_name=city_name,
	cycleways=cycleways,
	roads=roads,
	city_area=city_area)
```
#insert complete map

## The Best and Worst Bike Cities
My next goal was to find the best and worst bike infrastructures in major U.S. cities. I'm sure there are a lot of interesting network connectivity analyses I could use to quantify the quality of cycleway network. 

But for the time being, I decided a very straightforward approach would just be to calculate the ratio of roads to cycleways. If the ratio is 1:1, that means there's as much driving infrastructure as there is bike infrastructure. What a dream! 

To implement this, I used an OSMnx function called `basic_stats`. This returns useful info including the total length of the network edges. I can find the total length of the road network and bike network (in KM), and then calculate the ratio from that:
```Python
cycleways_stats = ox.basic_stats(cycleways)
roads_stats = ox.basic_stats(roads)

cycleway_length = cycleways_stats['edge_length_total']
roads_length = roads_stats['edge_length_total']

rc_ratio = roads_length/cycleway_length
``` 
I put that in a function called `calc_road_cycleway_ratio`. Then it was just a matter of putting everything together.

```Python
city_name =  'Chicago, IL'
# load city info and calculate road to cycleway ratio
cycleways,roads,city_area = get_city(city_name)
road_cycleway_ratio = calc_road_cycleway_ratio(cycleways, roads)

fig,ax = plot_cycleways(
	city_name=city_name,
	cycleways=cycleways,
	roads=roads,
	city_area=city_area,
	road_cycleway_ratio=road_cycleway_ratio)
```
#insert complete chicago map w ratio

I got a list of the top 30 most populous U.S. cities, and ran all of them through my pipeline. Here's the top and bottom 3 cities!
#insert top and bottom 3

Here's a bunch of other example maps. And here's a [complete collection](https://github.com/WilliamThyer/bike_networks/tree/master/examples/pdf) of maps I've generated.
#insert more map examples

