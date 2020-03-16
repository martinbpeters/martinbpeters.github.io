---
id: 6
title: "House Price Trends in Wicklow, Ireland with Docker, Pandas, PostGIS and QGIS"
date: 2020-03-15
excerpt: Study of Wicklow residential property transactions from 2010 to 2019. Set up PostGIS Locally with Docker Compose. Analysed data with Pandas/Matplotlib and spatial functions. Visualised results with QGIS.
category: postgis
tags: postgis postgres ireland property qgis
comments: true
published: true
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/cloudinary-core/2.8.0/cloudinary-core-shrinkwrap.js"></script>

# Intro
It's been a few weeks since my last post. Previously I focused on the 
introduction of 
[PostGIS concepts](https://www.martinpeters.ie/2020/02/02/postgis-basics/), 
and the [walking distance from the Luas](https://www.martinpeters.ie/2020/02/06/luas-walking-distance/). 
Here I'll analyse a property transactions dataset for County Wicklow in 
Ireland. I'll introduce Docker and how it can be used locally to store geo 
data, analyse it with Python [Pandas](https://pandas.pydata.org)/[Matplotlib](https://matplotlib.org) 
using spatial functions, and visualise with QGIS.

My wife and I owned a property in County Wicklow, Ireland from 2005 to 2019. 
In 2014, we became accidental landlords when be relocated to County Wexford. 
We'd followed the property market closely before selling in late 2019. We had 
purchased the property in 2005 close to the peak of the Celtic Tiger.

Since 2010, each property sale in the Republic of Ireland has been published 
on the [Property Price Register](https://www.propertypriceregister.ie). This 
dataset includes address and price information. In Wicklow at the time of 
writing had 12,698 residential transactions listed. I downloaded all records 
and to view these on a map or carry out spatial analysis I needed to geocode 
them. Geocoding is a process to convert addresses to locations (lat/lon). 
There are a number of freemium and commercial tools available for this 
including [Autoaddress](https://www.autoaddress.ie/), 
[Google](https://developers.google.com/maps/documentation/geocoding/intro),
and [HERE](https://developer.here.com/) to name just a few. I've used Google's 
geocoder as there's a $200 free monthly credit which allowed me to geocode the 
12k records free of charge.  

# Background

## Docker

[Docker](https://www.docker.com) allows you to build, run and share applications
with containers. Installation details can be found on the 
[Docker Documentation](https://docs.docker.com) page. An example image available 
on Docker hub is a [Postgres/Postgis Image](https://hub.docker.com/r/kartoza/postgis).
Using this you can have a database running within a container on your local machine.

After installing Docker. Create a folder (e.g. Wicklow_PPR) with a Dockerfile that 
has the following content:
```yaml
version: '3.2'
services:
  db:
    image: kartoza/postgis
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASS=superSecretPassword!
      - POSTGRES_DBNAME=db
      - POSTGRES_MULTIPLE_EXTENSIONS=plpgsql,postgis,hstore,postgis_topology,pgrouting,cube
      - ALLOW_IP_RANGE=0.0.0.0/0
    ports:
      - 25432:5432
    command: sh -c "echo \"host all all 0.0.0.0/0 md5\" >> /etc/postgresql/10/main/pg_hba.conf && /start-postgis.sh"
```

Then is as easy as running a docker-compose command which will create a 
container with a postgis-enabled postgres instance. Once spun up you can 
connect to it using your preferred SQL client or using the cli:
```console
# Docker Compose
docker-compose up -d --build db

# Access the database using the cli
psql --host localhost --port 25432 --username admin --dbname ddb
```

## Python with Pandas & Matplotlib
[Pandas](https://pandas.pydata.org) and [Matplotlib](https://matplotlib.org) 
are two popular Python packages for carrying out data analysis and visualising 
results. You can import various file types into Pandas including csv, xls, 
json etc. creating a so called dataframe. You can manipulate the dataframe 
like you would a sheet in Excel or table in SQL - selecting, filtering, 
joining, pivoting, filling missing data, aggregating etc.  

## QGIS
[QGIS](https://www.qgis.org/) is an open source Geographic Information System 
available available to [download](https://www.qgis.org/en/site/forusers/download.html) 
and install on all major operating systems. There's extensive 
[documentation](https://www.qgis.org/en/docs/index.html) on using it and on 
[GIS](https://docs.qgis.org/3.4/en/docs/gentle_gis_introduction/) 
in general.

QGIS can be used amongst other things to display points and polygons on top of base maps. 
The base map I've used here comes from [OpenStreetmap](https://www.openstreetmap.org/). 
Details of using [OSM Tile Servers](https://wiki.openstreetmap.org/wiki/Tile_servers) can
be found [here](https://www.xyht.com/spatial-itgis/using-openstreetmap-basemaps-qgis-3-0/).
The standard osm basemap includes roads and rail networks, and water features.
 
<p> 
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/v1584352266/github/QGIS_Wicklow_vrg96z.png" 
    class="cld-responsive">
</p>

# Dataset & Analysis
I ended up with two tables in the Postgis-enabled Postgres database. 
There's a one-to-one relationship between the the address field in 
the PPR table and the input_address field of the geocode table. 

<p>
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/github/PPR_Google_Geocode_vkdhed.png" 
    class="cld-responsive">
</p> 

Using just the PPR dataset we can see the price trends over time. 
I've use a [Box plot](https://en.wikipedia.org/wiki/Box_plot) to 
illustrate the trend (See code below). The median price (green line) has 
steadily improved from 2013 onwards. Unfortunately, I don't have data from the  
Celtic Tiger period (1995-2007) to compare. The quantity of transactions 
has also increased as more confidence in the market has returned and as more  
accidental landlords are now able to exit more easily. The PPR dataset 
also includes dimensions for Vat Exclusive and Not full market value 
(reasons not listed). New property transactions are exempt from VAT at 
13.5%.  

The size of the boxes were adjusted to reflect the number of transactions 
in the that year ([Sample size in Boxplots](https://stackoverflow.com/questions/29286217/is-there-a-good-way-to-display-sample-size-on-grouped-boxplots-using-python-matp)).  

<p>
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/github/Wicklow_TS_dhilp7.png" 
    class="cld-responsive">
</p> 

<div style="overflow-x:auto;">
    <table>
        <colgroup>
            <col width="40%" />
            <col width="30%" />
            <col width="30%" />
        </colgroup>
        <thead>
            <tr class="header">
                <th>Vat exclusive</th>
                <th>Not full market value</th>
                <th>Count</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>No</td>
                <td>No</td>
                <td>9,659</td>
            </tr>
            <tr>
                <td>Yes</td>
                <td>No</td>
                <td>2,392</td>
            </tr>
            <tr>
                <td>No</td>
                <td>Yes</td>
                <td> 477</td>
            </tr>    
            <tr>
                <td>Yes</td>
                <td>Yes</td>
                <td> 150</td>
            </tr>  
        </tbody>
    </table>
</div>

Taking a closer look at the geocoding results we can use the 
'location_type' field. The table below shows that over 65% of 
geocoding processes resulted in an exact location been returned.

Some notes on Google's Geocoder, I provided a bounding box:
```json
{
  "southwest": "51.0,-11.0", 
  "northeast": "56.0,-5.7"
}
```
and a region ("ie") to ensure Irish results were preferred 
([More details can be found here](https://developers.google.com/maps/documentation/geocoding/intro)). 
I also filtered out any record that did not return with Ireland as the 
value of country. More details of the code to geocode addresses 
will be the focus of another post.

<div style="overflow-x:auto;">
    <table>
        <colgroup>
            <col width="30%" />
            <col width="70%" />
        </colgroup>
        <thead>
            <tr class="header">
                <th>location_type</th>
                <th>Count</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>APPROXIMATE</td>
                <td>1,368</td>
            </tr>
            <tr>
                <td>GEOMETRIC_CENTER</td>
                <td>2,280</td>
            </tr>
            <tr>
                <td>RANGE_INTERPOLATED</td>
                <td> 744</td>
            </tr>    
            <tr>
                <td>ROOFTOP</td>
                <td>8,247</td>
            </tr>
        </tbody>
    </table>
</div>

Here are the transactions plotted on a map coloured by 'location_type' field. 
The APPROXIMATE results are often in the countryside where addresses are often
non-unique.

<p>
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/github/qgis_wicklow_address_types_dwyd8f.png" 
    class="cld-responsive">
</p> 

The analysis gets more interesting when we combine both the PPR and Geocoding 
results together. The price field is a continuous variable ranging from 
€11,000 to €1,850,000. To display this on a map it is convenient to convert to 
a dimension of price bins. Here we can easily see the most expensive 
properties are location around the Bray/Greystones area. 

<p>
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/github/qgis_wicklow_price_bins_t1hyny.png" 
    class="cld-responsive">
</p>

<p>
The bin sizes were automatically defined with QGIS using a graduated scheme of 10 evenly sized buckets:
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/github/qgis_wicklow_graduated_price_scale_jr2tgp.png" 
    class="cld-responsive">
</p>

There is some obvious clusters of property transactions within County Wicklow 
based around the population centres such as Bray, Greystones, Wicklow Town,
Arklow, Baltinglass, Blessington etc. Using a 
[k-means algorithm](https://postgis.net/docs/ST_ClusterKMeans.html) following 
an example on [stackexchange](https://gis.stackexchange.com/questions/11567/spatial-clustering-with-postgis/11778#11778) 
I clustered the data into 15 groups (arbitrarily set number) and I gave each 
group a label (largest town name). This allowed me to repeat the time series 
analysis but one level deeper.

```sql

CREATE OR REPLACE VIEW wicklow_clusters AS
SELECT
    kmean,
    count(*),
    ST_MinimumBoundingCircle(ST_Union(geom)) AS circle,
    sqrt(ST_Area(ST_MinimumBoundingCircle(ST_Union(geom))) / pi()) AS radius,
    ST_SetSRID(ST_Extent(geom), 4326) as bbox,
    ST_CollectionExtract(ST_Collect(geom),1) AS multipoint,
    ST_ConvexHull(ST_Collect(geom)) AS convexhull
FROM
(
    SELECT
           ST_ClusterKMeans(geom, 15) OVER() AS kmean,
           ST_Centroid(geom) AS geom
    FROM geocode g
    INNER JOIN ppr s ON s.id = g.nk
    WHERE
      source = 'ppr'
      AND s.address5 = 'Wicklow'
      AND g.administrative_area_level_1 = 'County Wicklow'
) tsub
GROUP BY kmean
ORDER BY count DESC;

CREATE TABLE wicklow_cluster_names (
  id integer,
  name varchar(64)
);

INSERT INTO wicklow_cluster_names(id, name)
VALUES
  ( 0, 'Baltinglass'),
  ( 1, 'Wicklow Town'),
  ( 2, 'Kiltegan'),
  ( 3, 'Shillelagh'),
  ( 4, 'Tinahely'),
  ( 5, 'Dunlavin'),
  ( 6, 'Aughrim'),
  ( 7, 'Arklow'),
  ( 8, 'Roundwood'),
  ( 9, 'Blessington'),
  (10, 'Rathdrum'),
  (11, 'Enniskerry'),
  (12, 'Newtownmountkennedy'),
  (13, 'Bray'),
  (14, 'Greystones');

-- Previously owned Greystones property dataset
SELECT
    s.id, s.date_of_sale, s.price, 
    s.not_full_market_value, 
    s.vat_exclusive, s.address_complete,
    g.geom,
    wcn.name as cluster_name
FROM geocode g
INNER JOIN ppr s ON s.id = g.nk
JOIN wicklow_clusters wc ON ST_Contains(wc.convexhull, g.geom)
INNER join wicklow_cluster_names wcn ON wcn.id = wc.kmean
WHERE
      source = 'ppr'
      AND s.address5 = 'Wicklow'
      AND administrative_area_level_1 <> 'County Wicklow'
      AND wcn.name = 'Greystones'
      AND vat_exclusive = 'No' AND not_full_market_value = 'No' -- 2nd Hand
;
```

<div style="overflow-x:auto;">
    <table>
        <colgroup>
            <col width="30%" />
            <col width="70%" />
        </colgroup>
        <thead>
            <tr class="header">
                <th>Cluster</th>
                <th>Count</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>Baltinglass</td>
                <td> 258</td>
            </tr>
            <tr>
                <td>Wicklow Town</td>
                <td>2,002</td>
            </tr>
            <tr>
                <td>Kiltegan</td>
                <td> 73</td>
            </tr>    
            <tr>
                <td>Shillelagh</td>
                <td> 89</td>
            </tr>
            <tr>
                <td>Tinahely</td>
                <td> 270</td>
            </tr>
            <tr>
                <td>Dunlavin</td>
                <td> 191</td>
            </tr>
            <tr>
                <td>Aughrim</td>
                <td> 246</td>
            </tr>
            <tr>
                <td>Arklow</td>
                <td>1,234</td>
            </tr>
            <tr>
                <td>Roundwood</td>
                <td> 181</td>
            </tr>
            <tr>
                <td>Blessington</td>
                <td> 620</td>
            </tr>
            <tr>
                <td>Rathdrum</td>
                <td> 427</td>
            </tr>
            <tr>
                <td>Enniskerry</td>
                <td> 257</td>
            </tr>
            <tr>
                <td>Newtownmountkennedy</td>
                <td> 848</td>
            </tr>
            <tr>
                <td>Bray</td>
                <td>2,354</td>
            </tr>
            <tr>
                <td>Greystones</td>
                <td>3,058</td>
            </tr>
        </tbody>
    </table>
</div>

<p>
<img 
    data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/github/qgis_wicklow_clusters_omj2qa.png" 
    class="cld-responsive">
</p>

Though only a couple of months into 2020, the median price trend is down from 
previous years and similar to 2017 levels. See the Greystones Box plot above.  

Note, this analysis does not take into account details of each property such 
as its condition, number of bedrooms/bathrooms, square footage, garden size, 
etc. Thus the trends in price should be taken with a pinch of salt. 

I hope this post illustrated some of the things you can do with free resources 
such as Postgres/PostGIS, Pandas & Matplotlib and QGIS.

# Code to generate the Box plots
requirements.txt file for python packages. 
```text
psycopg2
pandas 
python-dotenv==0.10.3
matplotlib
```

```python
import psycopg2
import sys, os
import numpy as np
import pandas as pd
import pandas.io.sql as psql
import matplotlib.pyplot as plt
from dotenv import load_dotenv, find_dotenv
import matplotlib as mpl

# .env file contains variables PGHOST, PGDATABASE, PGUSER, PGPORT and PGPASSWORD
load_dotenv(find_dotenv())

PGHOST = os.getenv('PGHOST')
PGDATABASE = os.getenv('PGDATABASE')
PGUSER = os.getenv('PGUSER')
PGPORT = os.getenv('PGPORT')
PGPASSWORD = os.getenv('PGPASSWORD')

conn_string = "host="+ PGHOST +" port="+ "5432" +" dbname="+ PGDATABASE +" user=" + PGUSER \
+" password="+ PGPASSWORD
conn=psycopg2.connect(conn_string)
cursor = conn.cursor()

def load_data():
    sql_command = """
    select
       id, date_of_sale,
       price, vat_exclusive, 
       not_full_market_value,
       date_part('YEAR', date_of_sale)::character varying as year
    from public.ppr
    where 
       address5 = 'Wicklow'
       and date_of_sale < '2020-01-01'
    ;
    """
    data = pd.read_sql(sql_command, conn)
    return (data)

df = load_data()
years = ['2010', '2011', '2012', '2013', '2014', '2015', '2016', '2017', '2018', '2019']

df_pivot = df.pivot(index='id', columns='year', values='price')
df_year = df.groupby('year')

counts = [len(v) for k, v in df_year]
total = float(sum(counts))
cases = len(counts)
widths = [c/total for c in counts]  

myFig2 = plt.figure(figsize=[12, 6])
cax = df_pivot.boxplot(column=years, widths=widths)
sample_sizes = ['%s\n$n$=%d'%(k, len(v)) for k, v in df_year]
cax.set_xticklabels(sample_sizes)
cax.get_yaxis().set_major_formatter(
  mpl.ticker.FuncFormatter(lambda x, p: format(int(x), ','))
)
axes = plt.axes()
axes.set_ylim(bottom=0, top=1000000)
```

<script type="text/javascript">
    my_breakpoints = function (width){
      return 50 * Math.ceil(width / 50);
    }
    var cl = cloudinary.Cloudinary.new({cloud_name: "mbp"});
    cl.config({breakpoints:my_breakpoints, responsive_use_breakpoints:"resize"});
    cl.responsive();
</script>

{% if page.comments %} 
{% include disqus.html %}
{% endif %}
