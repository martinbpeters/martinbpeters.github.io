---
id: 5
title: "Spatial analysis of Luas Stops"
date: 2020-02-06
excerpt: Spatial analysis of Luas Stops
category: postgis
tags: postgis postgres ireland aws
comments: true
published: true
---

In my last [blog post](https://www.martinpeters.ie/2020/02/02/postgis-basics/) I loaded some Irish datasets 
into a spatially aware database 
[Postgres + Postgis within RDS Aurora Serverless](https://github.com/martinbpeters/cdk-vpc-postgres) and introduced 
some spatial functions within Postgis. In this short post we'll use that data to answer some questions about the 
number of people living near the [Luas](https://luas.ie) in Dublin. (Note: [Luas](https://www.luas.ie) is a light rail 
network in Dublin, Ireland).

The number of people who live within 500 metres (5-8min walk) from a Luas stop in Dublin is close to 227,000 (based on 
[Small Area Census 2016](https://www.cso.ie/en/census/census2016reports/census2016smallareapopulationstatistics/)). I'm 
assuming every household in a small area is equidistance to each Luas stops.
```sql
WITH distinct_small_areas AS (
  SELECT DISTINCT sa.guid
  FROM public.small_areas sa
    JOIN public.luas_stops luas
      ON ST_DWithin(sa.wkb_geometry::geography, luas.wkb_geometry::geography, 500)
  WHERE
    luas.open = 'Yes'
    AND luas.type = 'Stop'
)
SELECT Sum(T1_2T::INTEGER)
FROM distinct_small_areas sa
  INNER JOIN public.sa_2016 ON sa_2016.guid = sa.guid;
-- 226,533
```

Let's plot the data to show the results of the query using a 
[dual axis chart in Tableau](https://help.tableau.com/current/pro/desktop/en-us/maps_dualaxis.htm). I'll use the 2019.2 
version of Tableau Desktop and publish to Tableau Public.  

<script src="https://cdnjs.cloudflare.com/ajax/libs/cloudinary-core/2.8.0/cloudinary-core-shrinkwrap.js"></script>
 
<p> 
  <img data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/v1580831915/github/luas_tableau_joins_jfho8a.png" class="cld-responsive">

<script type="text/javascript">
  my_breakpoints = function (width){
    return 50 * Math.ceil(width / 50);
  }
  var cl = cloudinary.Cloudinary.new({cloud_name: "mbp"});
  cl.config({breakpoints:my_breakpoints, responsive_use_breakpoints:"resize"});
  cl.responsive();
</script>
</p>

The `small_areas_near_luas` custom sql looks like the following. It returns a distinct list of small areas within 500m
of a luas stop. 
```sql
-- 
SELECT DISTINCT sa.guid, min(to_date(start, 'DD/MM/YYYY')) AS start_date
  FROM public.small_areas sa
    JOIN public.luas_stops luas
      ON ST_DWithin(sa.wkb_geometry::geography, luas.wkb_geometry::geography, 500)
WHERE
  luas.type = 'Stop'
  AND luas.open = 'Yes'
GROUP BY sa.guid
```
The full outer join with the `luas_stops` table allows us to plot the small area polygons and the luas stop locations 
creating the dual axis chart.

The dashboard below describes how the Luas developed over time from its initial launch in 2004 to its last extension 
in 2017 (the table below describes the quantity of people been able to access the luas by walking increase since 
2004 albeit using census 2016 data). Using the data slider on the RHS allows you to see the progression over time. The 
small areas within walking distance of the stops update as you move the slider in addition to the population metric on 
top. 

<div style="overflow-x:auto;">
    <table>
        <colgroup>
            <col width="30%" />
            <col width="70%" />
        </colgroup>
        <thead>
            <tr class="header">
                <th>Date</th>
                <th>Population</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>30/06/2004</td>
                <td> 57,410</td>
            </tr>
            <tr>
                <td>26/09/2004</td>
                <td>159,198</td>
            </tr>
            <tr>
                <td>08/12/2009</td>
                <td>168,293</td>
            </tr>    
            <tr>
                <td>16/10/2010</td>
                <td>184,609</td>
            </tr>
            <tr>
                <td>02/07/2011</td>
                <td>198,119</td>
            </tr>
            <tr>
                <td>09/12/2017</td>
                <td>226,533</td>
            </tr>
        </tbody>
    </table>
</div>

<div class='tableauPlaceholder' id='viz1580489690148' style='position: relative'>
  <noscript>
    <a href='#'>
      <img alt=' ' src='https://;public.tableau.com/static/images/7N/7N89MDKTZ/1_rss.png' style='border: none' />
    </a>
  </noscript>
  <object class='tableauViz'  style='display:none;'>
    <param name='host_url' value='https://public.tableau.com/' /> 
    <param name='embed_code_version' value='3' /> 
    <param name='path' value='shared/7N89MDKTZ' /> 
    <param name='toolbar' value='yes' />
    <param name='static_image' value='https://public.tableau.com/static/images/7N/7N89MDKTZ/1.png' /> 
    <param name='animate_transition' value='yes' />
    <param name='display_static_image' value='yes' />
    <param name='display_spinner' value='yes' />
    <param name='display_overlay' value='yes' />
    <param name='display_count' value='yes' />
    </object>
</div>
<script type='text/javascript'>
  var divElement = document.getElementById('viz1580489690148');
  var vizElement = divElement.getElementsByTagName('object')[0];
  if ( divElement.offsetWidth > 800 ) { 
    vizElement.style.width='1000px';
    vizElement.style.height='827px';
  } else if ( divElement.offsetWidth > 500 ) { 
    vizElement.style.width='1000px';
    vizElement.style.height='827px';
  } else { 
    vizElement.style.width='100%';
    vizElement.style.height='727px';
  }
  var scriptElement = document.createElement('script');
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>

## References
1. [Python CDK - VPC + Postgres](https://github.com/martinbpeters/cdk-vpc-postgres)
2. [Postgis](http://postgis.net)

{% if page.comments %} 
{% include disqus.html %}
{% endif %}
