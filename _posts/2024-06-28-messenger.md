
# How I recreated this timelapse of the MESSENGER flyby of Earth 

In 2005, NASA's MESSENGER spacecraft flew by Earth on its way to Mercury and as it departed, turned back to take 1080 
photos of Earth over a period of 24 hours. In that time it flew 370,287 km just beyond the orbit of the Moon.

![View of Earth from the Messenger probe as it flew by in 2006]({{site.baseurl}}/assets/images/EarthAnimation.gif)

Unfortunately, back in 2005, video compression technology was not particularly advanced and so when NASA released this 
timelapse, it was significantly lower in quality.

![NASA video of MESSENGER Earth flyby]({{site.baseurl}}/assets/video/PIA10120.mpeg)

## Pulling the archive image data

NASA makes most of its image publicly available via their [Planetary Data System](https://pds.nasa.gov/) or PDS. After
a bit of digging I found the image data from MESSENGER for this time period in 
[this directory](https://pdsimage2.wr.usgs.gov/Messenger/MSGRMDS_1001/). Each image is stored in two files an IMG image
and an XML metadata file. The IMG format is NASA's uncompressed 16bit image format. The XML file contains additional
metadata such as exposure time, ship position and attitude as well as filter information. In fact each frame of this
timelapse consists of 3 separate image take through different red, green and blue filters.

![View of Earth from the Messenger probe as it flew by in 2006]({{site.baseurl}}/assets/images/MessengerFrames.gif)

## Transforming the images

After downloading all the required images from the PDS archive, I had to convert them into a format that I could work
with. There are a few tools that allow you to manually open and manipulate IMG files such  
[NasaView](https://pds.nasa.gov/tools/about/pds3-tools/nasa-view.shtml) or [QGIS](https://www.qgis.org) but I ended up
using [GDAL](https://gdal.org/) an open source command line tool for transforming and working with all kinds of 
geospatial data formats. To convert the 1080 IMG files to 16bit TIFF, I wrote a script to run the following command:

```commandline
gdal_translate.exe -of GTiff -ot UInt16 -scale 0 4095 0 65535 <input>.xml <output>.tif
```

## Combining RGB

To combine 
https://github.com/alexspurling/messenger