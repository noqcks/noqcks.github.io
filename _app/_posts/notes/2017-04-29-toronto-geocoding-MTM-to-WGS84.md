---
layout: post
comments: true
title: Toronto Geocoding - MTM to WSG84
category: note
---

As someone without a degree in applied geography and without an understanding
of different coordinate reference systems, I had a hard time figuring out how to
convert Toronto address data to something I understand like latitude and
longitude.

The data that's given by the Open Data team at the City of Toronto is in the
coordinate system `MTM NAD 27(3 degree)` which lists coordinates in Easting and
Northing of a specific point.

<p><img src="{{ site.file }}/watermain_breaks.png" alt="Screenshot of watermain_breaks"></p>

This isn't very useful when you want to see locations on google maps which uses
the Longitude and Latitude coordinate system (formally called `WGS84`).

To convert form Easting/Northing (`MTM NAD 27`) to Long/Lat (`WGS84`) we can use a
python package called `proj`.

The `proj` package uses conversions between different coordinate systems using the [EPSG
database](http://support.esri.com/technical-article/000002814) of coordinate systems.
Each coordinate system has it's own designated number. To find the ESPG numbers for
`MTM NAD 27` and `WGS84` I just googled for them (e.g googled `EPSG WGS84` )

The numbers for each are

```
MTM NAD 27 -> EPSG:2019
WGS84      -> EPSG:4326
```

First, you will need to install pip and then install pyproj

```
pip install pyproj
```

Once installed, this simple script will convert coordinates to Long/Lat for you.

```
from pyproj import Proj, transform

## spatial reference system ##
# from MTM NAD 27
inProj = Proj(init='epsg:2019')
# to WGS84
outProj = Proj(init='epsg:4326')

x1,y1 = 319556.408,4840591.946
x2,y2 = transform(inProj,outProj,x1,y1)
print(x2,y2)

[OUT]> -79.3169043897595 43.707075474849695
```

Hope it helped you! ^__^

