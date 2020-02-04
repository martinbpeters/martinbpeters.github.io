---
id: 2
title: "Geospatial Data Analysis in Ireland"
date: 2020-01-25
excerpt: Irish Geospatial Data Analysis
category: geospatial
tags: geospatial ireland
comments: true
published: true
---

Geospatial analysis is the study of information with a spatial component. This could include the birth rates by country 
or number of All-Ireland's won by each county (more on that in a later post). The requirements for geospatial analysis 
is one or more metrics (e.g. population or average age) and one or more geo dimensions (location name, latitude and 
longitude (lat/long) pairs or polygons to define shapes). With labels and metrics you can generate simple charts like 
bar (see below a simple bar chart of the population of Irish cities in 2016) or scatter. But here I’ll focus on using 
the geo data like coordinates or polygons. 

<script src="https://code.highcharts.com/highcharts.js"></script>
<script src="https://code.highcharts.com/modules/exporting.js"></script>
<script src="https://code.highcharts.com/modules/export-data.js"></script>
<script src="https://code.highcharts.com/modules/accessibility.js"></script>

<figure class="highcharts-figure">
    <div id="container"></div>
    <p class="highcharts-description">
        Chart showing the population of irish cities from the 2016 census. 
        <a href="https://www.highcharts.com/demo/column-rotated-labels">(Highcharts bar chart)</a>
    </p>
</figure>

<script>
Highcharts.chart('container', {
    chart: {
        type: 'column'
    },
    title: {
        text: 'Largest cities in Ireland per 2016'
    },
    subtitle: {
        text: 'Source: <a href="https://en.wikipedia.org/wiki/List_of_urban_areas_in_the_Republic_of_Ireland_by_population">Wikipedia</a>'
    },
    xAxis: {
        type: 'category',
        labels: {
            rotation: -45,
            style: {
                fontSize: '13px',
                fontFamily: 'Verdana, sans-serif'
            }
        }
    },
    yAxis: {
        min: 0,
        title: {
            text: 'Population (thousands)'
        }
    },
    legend: {
        enabled: false
    },
    tooltip: {
        pointFormat: 'Population in 2016: <b>{point.y:.1f} thousands</b>'
    },
    series: [{
        name: 'Population',
        data: [
            ['Dublin', 1173.1],
            ['Cork', 208.6],
            ['Limerick', 94.1],
            ['Galway', 79.9],
            ['Waterford', 53.5],
            ['Drogheda', 40.9],
            ['Swords', 39.2],
            ['Dundalk', 39.0],
            ['Bray', 32.6],
            ['Navan', 30.1]
        ],
        dataLabels: {
            enabled: true,
            rotation: -90,
            color: '#FFFFFF',
            align: 'right',
            format: '{point.y:.1f}',
            y: 10,
            style: {
                fontSize: '11px',
                fontFamily: 'Verdana, sans-serif'
            }
        }
    }]
});
</script>

Some examples of point datasets include residential property (every EirCode has a lat/long associated with it, more on 
EirCodes in a later post), amenities (schools, restaurants, airports etc.) and real-time vehicle locations. These 
discrete points can be displayed on top of a base map. The base map can display things like roads and rail networks, 
water features or heatmaps. More details of storing geojson on github can be found 
[here](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github).

<div class="iframely-embed">
  <div class="iframely-responsive" style="padding-bottom: 56.2493%;">
    <a href="https://gist.github.com/martinbpeters/39f26dbcaabd3d6ead9c7d5b347cd42d" data-iframely-url="//cdn.iframe.ly/oTLZjFD"></a>
  </div>
</div>
<script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

[Choropleths](https://en.wikipedia.org/wiki/Choropleth_map) or heatmaps are charts where polygons or shapes are layered 
onto a map and are often colored to represent the associated metrics (In the county chart below they are coloured based 
on [GAA playing colours](https://www.discoveringireland.com/the-colors-of-the-counties-of-ireland/)). Each shape is 
assigned a colour based on the value of the metrics. This approach allows the analyst to visually compared the 
neighboring areas. An example here would be the population of each country in the world. There are many available 
polygon datasets: countries, provinces, counties etc.

<div class="iframely-embed">
  <div class="iframely-responsive" style="padding-bottom: 56.2493%;">
    <a href="https://gist.github.com/martinbpeters/34e258dadca967393291b7a128857350" data-iframely-url="//cdn.iframe.ly/yNtPzpA"></a>
  </div>
</div>
<script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

In the Republic of Ireland there are many [datasets](https://data.gov.ie/organization/ordnance-survey-ireland) to 
subdivide the country including electoral, municipal and authoritative (geography levels). These include 
[Electoral Divisions](https://data.gov.ie/dataset/cso-electoral-divisions-ungeneralised-osi-national-statistical-boundaries-2015) 
(EDs, 3,409 legally defined administrative areas in the State), [Small Area](https://data.gov.ie/ga/dataset/census-small-area) 
(18,488 polygons each with 65-90 households. Subdivisions of EDs), 
[Settlements](https://data.gov.ie/dataset/settlements-ungeneralised-osi-national-statistical-boundaries-2015) 
(distinguish between the urban and rural population for census analysis), town and city boundaries, baronies, civil 
parishes, etc.

A newer dataset is [Routing Keys](https://www.autoaddress.ie/blog/autoaddressblog/2016/09/21/eircode-routing-keys) 
from the [EirCode](https://www.eircode.ie/) initiative - Ireland's postcode system launched in July 2015. Each [EirCode](https://www.eircode.ie/what-is-eircode) 
has 7 characters and the first 3 represent the routing key e.g. Y25. The last 4 characters is the unique identifier. 
There are 139 Routing Keys in total. Each routing key is a shape and can be represented on a map like the following:

<div class="iframely-embed">
  <div class="iframely-responsive" style="padding-bottom: 56.2493%;">
    <a href="https://gist.github.com/martinbpeters/81fe8816a3d800b794874ca0d2457373" data-iframely-url="//cdn.iframe.ly/difr0sv"></a>
  </div>
</div>
<script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

Now we have the shapes or polygons for areas in Ireland we need to source metrics to display. The 
[Irish Government](https://data.gov.ie) and [Central Statistics Office](http://www.cso.ie) provide data on things 
like birth and unemployment rates, population, crime statistics to name but a few. 

By adding a temporal dimension into the mix you can see how the heatmap changes over time. For example, how the 
population of Irish counties changed from one census to the next (1841 to 2016, 
[Census Data](https://data.gov.ie/dataset/population-at-each-census-1841-to-2016-number-by-county-sex-and-censusyear)). 
People in Ireland have been moving towards the capital city since the Great Famine from 1845 to 1849.  

{:refdef: style="text-align: center;"}
![Population by County over time](/static/gifs/population_county_time_series_320.gif)
{: refdef}
See the original visualisation on 
[Tableau Public](https://public.tableau.com/views/population_county_time_series/Map?:display_count=y&publish=yes&:origin=viz_share_link).

<script src="https://cdnjs.cloudflare.com/ajax/libs/cloudinary-core/2.8.0/cloudinary-core-shrinkwrap.js"></script>

An alternative view of the data can be seen here in stacked bar format:
<p> 
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/github/population_county_time_series_abmzht.png" 
    class="cld-responsive">

<script type="text/javascript">
    my_breakpoints = function (width){
      return 50 * Math.ceil(width / 50);
    }
    var cl = cloudinary.Cloudinary.new({cloud_name: "mbp"});
    cl.config({breakpoints:my_breakpoints, responsive_use_breakpoints:"resize"});
    cl.responsive();
</script>
</p>

Hopefully this short blog post has introduced some of the terms in geospatial analysis. In future posts I'll introduce 
how you can handle spatial data in open source databases (Postgres with PostGIS) and in the cloud with AWS and RDS. 
I'll go into more detail on polygon and point datasets, simple routing from point-to-point and ways to visualise spatial
data with tools like Tableau/QGIS and web frameworks like d3js.

{% if page.comments %} 
{% include disqus.html %}
{% endif %}