---
id: 5
title: "Spatial analysis of Luas Stops"
date: 2020-02-06
excerpt: Spatial analysis of Luas Stops
category: postgis
tags: postgis postgres ireland aws
comments: true
published: false
---

In my last [blog post](https://www.martinpeters.ie/2020/02/02/postgis-basics/) I loaded some Irish datasets 
into a spatially aware database (Postgres + Postgis within RDS Aurora Serverless) and introduced some spatial functions
within Postgis. In this short post we'll use that data to answer some questions about the number of people living 
near the [Luas](https://luas.ie) in Dublin. 

The number of people who live within 500 metres (5min walk) from a Luas stop in Dublin is close to 200,000.
```sql
WITH distinct_small_areas AS (
  SELECT DISTINCT sa.guid
  FROM public.small_areas sa
    JOIN public.luas_stops luas
      ON ST_DWithin(sa.wkb_geometry::geography, luas.wkb_geometry::geography, 500)
)
SELECT Sum(T1_2T::INTEGER)
FROM distinct_small_areas sa
  INNER JOIN public.sa_2016 ON sa_2016.guid = sa.guid;
-- 198,674
```

Let's plot the data to show the results of the query using a 
[dual axis chart in Tableau](https://help.tableau.com/current/pro/desktop/en-us/maps_dualaxis.htm)
```sql
-- small_areas_near_luas custom sql
SELECT DISTINCT sa.guid
  FROM public.small_areas sa
    JOIN public.luas_stops luas
      ON ST_DWithin(sa.wkb_geometry::geography, luas.wkb_geometry::geography, 500)
```

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
