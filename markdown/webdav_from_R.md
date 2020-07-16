<a href="https://www.isric.org" rel="isric.org"> <img src="https://www.isric.org/themes/custom/basic/logo.svg"  height="130"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="https://soilgrids.org" rel="soilgrids.org"> <img src="https://www.isric.org/sites/default/files/styles/gallery_big_image_900x700/public/SoilGrids_banner_web.png"  height="130">

# [WebDAV](https://files.isric.org/soilgrids/data/recent/): direct access to the maps in VRT format.

### R
#### Load libraries, select boundary box and cell size

Initially you need to load the following libraries and select the bounding box of the area you are interested in:

```R
library(rgdal)
library(gdalUtils)
bb=c(-337500.000,1242500.000,152500.000,527500.000) # Example bounding box (homolosine)
igh='+proj=igh +lat_0=0 +lon_0=0 +datum=WGS84 +units=m +no_defs' # proj string for Homolosine projection

sgurl=
```

#### To a geotiff in Homolosine
This GDAL command will create a local geotiff in the Homolosine projection

```
gdal_translate('/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data/ocs/ocs_0-30cm_mean.vrt',
    "./crop_roi_igh_r.tif",
    tr=c(250,250),
    projwin=bb,
    projwin_srs =igh,
    verbose=TRUE)
```

#### To a geotiff in a different projection
The following commands describe a workflow to obtain a VRT or a GeoTiff for an area of interest in a projection of your choice. In this example we will use EPSG=4326

##### To local VRT in homolosine (directly from the webdav connection)
The first step is to obtain a VRT for the area of interest in the Homolosine projection. We suggest to use VRT for the intermediate steps to save space and computation times.

```
gdal_translate('/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data/ocs/ocs_0-30cm_mean.vrt',
    "./crop_roi_igh_r.vrt",
    of="VRT",tr=c(250,250),
    projwin=bb,
    projwin_srs =igh,
    verbose=TRUE)
```

##### To a VRT in, for example, LatLong
The following command will generate a VRT in the projection of your choice:

```
gdalwarp("./crop_roi_igh_r.vrt",
    "./crop_roi_ll_r.vrt", 
    s_src=igh, 
    t_srs="EPSG:4326", 
    of="VRT")
```

##### To a final Geotiff
The following command will generate a Geotiff in the projection of your choice for the area of interest defined above

```
gdal_translate("./crop_roi_ll_r.vrt",  
    "./crop_roi_ll_r.tif", 
    co=c("TILED=YES","COMPRESS=DEFLATE","PREDICTOR=2","BIGTIFF=YES"))
```

#### Read in R
Finally you can read any of the generated VRTs or GeoTiffs in R for further analysis

```
r=readGDAL("./crop_roi_igh_r.vrt") # Or any other of the files produce above
```
​
