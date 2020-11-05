---
title: 'Interactive generative line art app using Bokeh and Numpy'
date: 2020-11-05
permalink: /posts/2020/11/locations_of_lines/
tags:
  - generative
  - python
  - bokeh
---

I created an interactive generative line art creator in Python with the Bokeh library. I was inspired by the "Locations of Lines" artworks by Sol LeWitt I recently saw at the Chicago Museum of Contemporary Art. 

Bokeh is great because it allows you to create D3 style interactive plots within Python. So this was a great opportunity to try out the library and get a little creative with Numpy arrays. Before I get into the code, I'll show the final result:

![](https://williamthyer.github.io/images/locations_of_lines/locations_of_lines.gif)

I wanted to be able to change several aspects of the artwork. Line length and gap length, line width, row and column density, and the line colors. This was a wide variety of parameters that would need to do some pretty different things in the backend. For example, changing line thickness is trivial, but changing line length involves completely modifying the underlying data that is being plotted.

## Creating a single row or column of data
First, I'll outline how I create the data row a single row of lines. Each line needs a start and end point, with each point having an X and Y coordinate. To create the X coordinates, I just need the line length and gap length. Then, with the help of some fancy Numpy indexing, I can create two arrays: the X-coordinates for start of each line and the X-coordinates for the end of each line. The Y coordinates are easy since we're only creating lines for one row. They're all just the same row number.

```python
# creating row of lines
```
##insert image? numpy array

To create columns of data, I simply swap the X and Y coordinates. Originally I had two sets of functions for creating rows and columns. But I realized they were 99% identical functions so I merged them.

## Creating all rows/columns of data
Next, I needed to go row-by-row and create each row of data. Here's where row and column density come into play. Basically, I wanted to be able to manipulate how close each row/column was to each other. This is easily implemented by iterating over the rows while using the density as the step size. So, create one row, skip 4, and create another. I love Numpy. 

```python
# creating rows of data
```
## insert image of basic plot

## Plotting with Bokeh
Okay, so now that I have the data, I want to plot it. Bokeh is very well-documented, so plotting was pretty easy. Now that I have experience in Matplotlib, ggplot2 in R, and MATLAB plotting, I feel pretty confident going into new plotting libraries.

I used the `MultiLine` function to plot all of the rows at once. Then a second call plots all of the columns. 

```python
# plotting 
```
## insert basic plot

## Adding interactivity using Bokeh server
Here's the really interesting part. The Bokeh server allows me to create a plot with widgets that implement code to update the data that is being plotted. Changing line length, gap length, and density requires the data to be updated on-the-fly as the widget is used. The color picker and line thickness are much easier to implement since they are just plotting aesthetics.

For the widgets that affect the data, I needed to first create them:

```python
create widgets
```







