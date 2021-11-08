# [proposal-geojson maps for guest locational behavior]

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

- manually

```ts
const str = "-_-abc-_-";
const characters = "-_";

let start = 0;
while (characters.indexOf(str[start]) >= 0) {
    start += 1;
}
let end = str.length - 1;
while (characters.indexOf(str[end]) >= 0) {
    end -= 1;
}

console.log(str.substr(start, end - start + 1)) // abc
```

As you can see. For these both solutions, there's no idea about what does it doing when you see it, You have to pay attention on it to understand it. 

### Performance

And for the regex version, there's might also performance issue as [TypeScript's implementations](https://github.com/microsoft/TypeScript/blob/main/src/compiler/core.ts#L2330-L2344): [jsbench](https://jsbench.me/gjkoxld4au/1).

### Consolation

That's why we need this proposal. It's will add some **semantic** and **convenience** way to `clearly representing the operation i want`. And as a possible bonus, it also reduces the amount of very poorly performing code we write.

## Core API

Add an optional argument `characters` into `String.prototype.trim` ,  `String.prototype.trimStart`and   `String.prototype.trimEnd`. 

This argument will allow us which characters will be remove from the start or end (or both) from the string.

The definition of API will looks like:

```ts
interface String {
    trim(characters?: string): string;
    trimStart(characters?: string): string;
    trimEnd(characters?: string): string;
}

```

With this proposal, we could use as:

```typescript
const str = "-_-abc-_-";
const characters = "-_";

console.log(str.trim(characters)) // abc
console.log(str.trimStart(characters)) // abc-_-
console.log(str.trimEnd(characters)) // -_-abc
```

## Prior art

- Lodash - [lodash.trim](https://lodash.com/docs/4.17.15#trim), [lodash.trimStart](https://lodash.com/docs/4.17.15#trimStart), [lodash.trimEnd](https://lodash.com/docs/4.17.15#trimEnd)
- PHP - [function.trim](https://www.php.net/manual/en/function.trim.php), [function.ltrim](https://www.php.net/manual/en/function.ltrim.php), [function.rtrim](https://www.php.net/manual/en/function.rtrim.php)
- Python - [str.strip](https://docs.python.org/3/library/stdtypes.html#str.strip), [str.lstrip](https://docs.python.org/3/library/stdtypes.html#str.lstrip), [str.rstrip](https://docs.python.org/3/library/stdtypes.html#str.rstrip)
- C# - [String.Trim](https://docs.microsoft.com/en-us/dotnet/api/system.string.trim?view=net-5.0), [String.TrimStart](https://docs.microsoft.com/en-us/dotnet/api/system.string.trimstart?view=net-5.0), [String.TrimEnd](https://docs.microsoft.com/en-us/dotnet/api/system.string.trimend?view=net-5.0)
- Go - [Trim](https://golang.org/pkg/strings/#Trim), [TrimLeft](https://golang.org/pkg/strings/#TrimLeft), [TrimRight](https://golang.org/pkg/strings/#TrimRight), [TrimPrefix](https://golang.org/pkg/strings/#TrimPrefix), [TrimSuffix](https://golang.org/pkg/strings/#TrimSuffix)

## Previous discuss
- https://github.com/tc39/proposal-string-left-right-trim/issues/22 
- https://esdiscuss.org/topic/string-trim-chars 
- https://esdiscuss.org/topic/string-prototype-trimstart-string-prototype-trimend-with-a-given-string

## Proposer

Champions:

- @Kingwl (Wenlu Wang, KWL)

