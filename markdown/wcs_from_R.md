<a href="https://www.isric.org" rel="isric.org"> <img src="https://www.isric.org/themes/custom/basic/logo.svg"  height="130"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="https://soilgrids.org" rel="soilgrids.org"> <img src="https://www.isric.org/sites/default/files/styles/gallery_big_image_900x700/public/SoilGrids_banner_web.png"  height="130">

# Access WCS with R
This tutorial will show how to access SoilGrids using Web Coverage Services and the R software. This tutorial will use the `rgdal` and `gdalUtils` packages.

This tutorial works with a gdal version of 2.3.x or newer.

## Load libraries

```R
library(XML)
library(rgdal)
library(gdalUtils)
```

## Set variables of interest

We define the variables for the soil property and layer of interest. See [here](https://www.isric.org/explore/soilgrids/faq-soilgrids#What_do_the_filename_codes_mean) for the naming conventions

```R
voi = "nitrogen" # variable of interest
depth = "5-15cm"
quantile = "Q0.5"

voi_layer = paste(voi,depth,quantile, sep="_") # layer of interest 
```

We set other variables necessary for the WCS call for all kinds of requests

```R
wcs_path = paste0("https://maps.isric.org/mapserv?map=/map/",voi,".map") # Path to the WCS. See maps.isric.org
wcs_service = "SERVICE=WCS"
wcs_version = "VERSION=2.0.1" # Only for gdal >=2.3. Use wcs_version = "VERSION=1.1.1"` for gdal < 2.3.x
```
**NOTE**
Use `wcs_version = "VERSION=2.0.1"` for gdal >= 2.3.x and `wcs_version = "VERSION=1.1.1"` for gdal < 2.3.x

## Example 1: Describe the coverage layer
###### 1. First we define the request as `DescribeCoverage` and we create a string for the full request using also the variables previously defined

```R
wcs_request = "DescribeCoverage" 

wcs = paste(wcs_path, wcs_service, wcs_version, wcs_request, sep="&")
```

###### 2. Then we create a XML that can be used with the `gdalinfo` utility after being saved to disk:

```R
l1 <- newXMLNode("WCS_GDAL")
l1.s <- newXMLNode("ServiceURL", wcs, parent=l1)
l1.l <- newXMLNode("CoverageName", voi_layer, parent=l1)

# Save to local disk
xml.out = "./sg.xml"
saveXML(l1, file = xml.out)
```

###### 3. Finally we use `gdalinfo` to get the description of the layer

```R
gdalinfo("./sg.xml")
```

## Example 2: Download a Tiff for a region of interest (ROI)

###### 1. First we define the request with the parameters for the region of interest, projection, format, resolution

An example using Homolosine projection

```R
bb=c(-337500.000,1242500.000,152500.000,527500.000) # Example bounding box (homolosine)
igh='+proj=igh +lat_0=0 +lon_0=0 +datum=WGS84 +units=m +no_defs' # proj string for Homolosine projection
```

We paste all the components of the WCS request:

```R
wcs = paste(wcs_path,wcs_service,wcs_version,sep="&")

```

###### 2. Then we create a XML that can be used with the `gdal` utility after being saved to disk:
```R
l1 <- newXMLNode("WCS_GDAL")
l1.s <- newXMLNode("ServiceURL", wcs, parent=l1)
l1.l <- newXMLNode("CoverageName", "nitrogen_5-15cm_Q0.5", parent=l1)

# Save to local disk
xml.out = "./sg.xml"
saveXML(l1, file = xml.out)
```
###### 3. Finally we use `gdal_translate` to get the geotiff locally.

```R
# Download raster as GeoTIFF (Warning: it can be large!)
file.out <- './test.tif'

gdal_translate(xml.out, file.out,
    tr=c(250,250), projwin=bb,
    projwin_srs =igh, co=c("TILED=YES","COMPRESS=DEFLATE","PREDICTOR=2","BIGTIFF=YES"),
    verbose=TRUE
)
```

The downloaded file can then be read into R. 


See [this page](https://www.isric.org/explore/soilgrids/soilgrids-access) for examples on how to reproject 
