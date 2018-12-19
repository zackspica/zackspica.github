---
title: "The Stanford University Map and California Outline with Cartopy"
layout: post
comments: true
use_code: true
---

I recently got involved in Fiber-Optic seismology using Distributed Acoustic Sensing (DAS). With DAS, the number of sensors used is an order of magnitude higher than with conventional seismic experiment and the sensor spacing may be at the scale of 1 m. 
With such technology, detailed mapping of urban area must be provided to understand which sensor probes which region. [Cartopy](https://scitools.org.uk/cartopy/docs/latest/) offers an amazing tool to do so and here is an example: 

```py
import matplotlib.pyplot as plt
from pyrocko import model
import cartopy.crs as ccrs
from cartopy.io.img_tiles import OSM
from cartopy.mpl.gridliner import LONGITUDE_FORMATTER, LATITUDE_FORMATTER
# Pyrocko is an awsome package for seismology - https://pyrocko.org/
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

That's it. Now let's try to add to outline of California out of the main plot to better locate where Stanford University is. Although it sounds simple it took me quiet a long time to figure out which file to download and how to deal with it. I got inspired by [this caropy tuto](https://scitools.org.uk/cartopy/docs/v0.15/examples/hurricane_katrina.html). 

```py
import cartopy.io.shapereader as shpreader
# Create a new ax for California
ax2 = fig.add_axes([0.75,.13,.25,.25], projection=ccrs.Mercator())
ax2.set_extent([-126, -114, 32, 42], ccrs.Geodetic())  
# This is the file you actually need
shapename = 'admin_1_states_provinces_lakes_shp'
# Then cartopy does the rest
states_shp = shpreader.natural_earth(resolution='110m',category='cultural', name=shapename)
# And here is how to properly read the file
# It is divide in geometries (xy) and the records (tags)
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

![](https://zackspica.github.com/images/map.png)

Hope it helps! Such a simple post would have save me a lot of time at least. 


