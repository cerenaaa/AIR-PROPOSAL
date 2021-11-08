# Proposal-geojson maps for guest locational behavior

Proposal to add Plotly (or other) maps to AIR for locational visibility on campaigns, redemptions, offer density and more.

## Status

This proposal is a [stage-0 proposal] and waiting for feedback.

## Motivation

Customers often ask if our campaigns create bottlenecks in their understaffed stores. More generally, a geospatial view of which stores SS Campaigns affect could be useful in understanding the distribution of our guest selection and redemption behavior.

If our customers would like to have the flexibility to select regions to launch campaigns in, this visual can help them with that. Eventually, the idea is to augment Smart Segments with a geospatial configuration. 

### Initial Views
What initial views/aggregations would be most useful to customers?

-State/county level store locations with their guest density
-Zip level store locations with their guest density
-DMA level store locations with their guest density (potential future state)

-State/county level store locations with their guest spend
-Zip level store locations with their guest spend
-DMA level store locations with their guest spend (potential future state)

-State/county level campaign targeting/redemptions
-Zip level campaign targeting/redemptions
-DMA level campaign targeting/redemptions (potential future state)

-If data is available, we can also layer store capacity.

- plotly example

```ts
from urllib.request import urlopen
import json
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

import plotly.express as px

df_fip_pd=(df_fip
           .groupby([ 'COUNTY']).agg(f.round(f.sum('item_subtotal'),1).alias('total_spend'))
           .toPandas()
          )

fig = px.choropleth(df_fip_pd, geojson=counties, locations='COUNTY', color='total_spend',
                           color_continuous_scale=px.colors.diverging.BrBG,
                          # range_color=(0, 50),
                              scope="usa",
                           labels={'total_spend':'$'},
#                     facet_col='category_1', facet_col_wrap=3,
#                     facet_col_spacing=0.01,
                   basemap_visible=True
                    
                    
                          )
fig.update_geos(fitbounds="locations", visible=True)
fig.update_yaxes(matches=None)
fig.for_each_annotation(lambda a: a.update(text=a.text.split("=")[-1]))
fig.update_yaxes(showticklabels=True) # assuming second facet


fig.show()
```

- visual

![image](https://user-images.githubusercontent.com/37112850/140806448-0d75a4bc-8182-4f76-8505-cc35c981500c.png)

![non_seco_spend_by_region](https://user-images.githubusercontent.com/37112850/140809205-7f519d52-5e6d-4c53-b3e0-4877b4d5cb77.png)



