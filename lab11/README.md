Lab 11 - Interactive Visualization
================
11/12/2021


**HTML report** [**click here**](https://rawcdn.githack.com/vivi0903/PM566lab/3fdaea645fb084026ca752b892e657996af946e4/lab11/11-lab.html)

**And remember to set `eval=TRUE`**

# Learning Goals

-   Read in and process the COVID dataset from the New York Times GitHub
    repository
-   Create interactive graphs of different types using `plot_ly()` and
    `ggplotly()` functions
-   Customize the hoverinfo and other plot features
-   Create a Choropleth map using `plot_geo()`

# Lab Description

We will work with COVID data downloaded from the New York Times. The
dataset consists of COVID-19 cases and deaths in each US state during
the course of the COVID epidemic.

**The objective of this lab is to explore relationships between cases,
deaths, and population sizes of US states, and plot data to demonstrate
this**

# Steps

## I. Reading and processing the New York Times (NYT) state-level COVID-19 data

### 1. Read in the data

-   Read in the COVID data with data.table:fread() from the NYT GitHub
    repository:
    b