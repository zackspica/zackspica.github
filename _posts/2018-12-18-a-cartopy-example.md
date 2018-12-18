---
title: "The Stanford University map with cartopy"
layout: post
comments: true
use_code: true
---

I recently got involved in Fiber-Optic seismology using Distributed Acoustic Sensing (DAS). With DAS, the number of sensors used is an order of magnitude higher than with conventional seismic experiment and the sensor spacing may be at the scale of 1 m. 
With such technology detailed mapping of urban area must be provide to understand which sensor proves which region. Cartopy offers an amazing tool to do so and here is an example: 

```py
import matplotlib.pyplot as plt
from pyrocko import model
import cartopy.crs as ccrs
from cartopy.io.img_tiles import OSM
from cartopy.mpl.gridliner import LONGITUDE_FORMATTER, LATITUDE_FORMATTER
# Pyrocko is an awsome package for seismology
stations = model.load_stations('stations.txt')
# Fig setting
fig = plt.figure()
fig.subplots_adjust(left=0.04)
# These 3 lines are the core of the code
imagery = OSM()
ax = plt.axes(projection=imagery.crs)
ax.set_extent((-122.179436, -122.171321, 37.425548, 37.431271))
# Add the imagery to the map. The second argument refers to the 
# definition of the map downloaded.
ax.add_image(imagery, 17)
# Some fancyness
gl = ax.gridlines(crs=ccrs.PlateCarree(), draw_labels=True, linewidth=0, \
        color='gray', alpha=0.5, linestyle='--')
gl.xlabels_top = False
gl.ylabels_right = False
gl.xformatter = LONGITUDE_FORMATTER
gl.yformatter = LATITUDE_FORMATTER
gl.xlabel_style = {'size': 9,'rotation':30}
gl.ylabel_style = {'size': 9,}
# Plot the locations of only the 300 first stations
for i, sta in enumerate(stations):
    if i < 301: plt.scatter(sta.lon, sta.lat, transform=ccrs.Geodetic(),color='k',s=1)
```

That's it. Now let's try to add to outline of California out of the main plot to better locate where Stanford University is. Although it sounds simple it took me quiet a long time to figure out which file to download and how to deal with it. I got inspired by [this tuto](https://scitools.org.uk/cartopy/docs/v0.15/examples/hurricane_katrina.html). 

```py
ax2 = fig.add_axes([0.75,.13,.25,.25], projection=ccrs.Mercator())
ax2.set_extent([-126, -114, 32, 42], ccrs.Geodetic())  
# This is the file you actually need
shapename = 'admin_1_states_provinces_lakes_shp'
# Then cartopy does the rest
states_shp = shpreader.natural_earth(resolution='110m',category='cultural', name=shapename)


reader = shpreader.Reader(states_shp)
records = reader.records()
geometries = reader.geometries()
for state_record, state_geometry in zip(records, geometries):
    if state_record.attributes['name'] == 'California':
        ax2.add_geometries([state_geometry], ccrs.PlateCarree(), facecolor='none', edgecolor='k')
# To get the effect of having just the states without a map "background"
# turn off the outline and background patches
ax2.background_patch.set_visible(False)
ax2.outline_patch.set_visible(False)
ax2.scatter(-122.175,37.428, transform=ccrs.Geodetic(), color='#8C1515', marker='*',s=50)
ax2.text(-122, 38, 'California', rotation=-45, transform=ccrs.Geodetic())

```



[Link to another page: About]({{ site.baseurl }}/about).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# [](#header-1)Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## [](#header-2)Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### [](#header-3)Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### [](#header-4)Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### [](#header-5)Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### [](#header-6)Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
