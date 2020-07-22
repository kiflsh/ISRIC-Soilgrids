<a href="https://www.isric.org" rel="isric.org"> <img src="https://www.isric.org/themes/custom/basic/logo.svg"  height="130"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="https://soilgrids.org" rel="soilgrids.org"> <img src="https://www.isric.org/sites/default/files/styles/gallery_big_image_900x700/public/SoilGrids_banner_web.png"  height="130">

# [WebDAV](https://files.isric.org/soilgrids/data/recent/): direct access to the maps in VRT format.

### Linux Bash
#### Select boundary box and cell size

Initially you need to import the following package and select the bounding box of the area you are interested in:

```bash
BOUNDS="-337500.000 1242500.000 152500.000 527500.000" # Example bounding box (homolosine) for Ghana
ulx uly lrx lry
CELL_SIZE="250 250"

IGH="+proj=igh +lat_0=0 +lon_0=0 +datum=WGS84 +units=m +no_defs" # proj string for Homolosine projection
SG_URL="/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data"

```

#### To a geotiff in Homolosine
This GDAL command will create a local geotiff in the Homolosine projection

``` bash
gdal_translate -projwin $BOUNDS -projwin_srs "$IGH" -tr $CELL_SIZE \
    -co "TILED=YES" -co "COMPRESS=DEFLATE" -co "PREDICTOR=2" -co "BIGTIFF=YES" \
    $SG_URL"/ocs/ocs_0-30cm_mean.vrt" \
    "ocs_0-5cm_mean.tif"
```

#### To a geotiff in a different projection
The following commands describe a workflow to obtain a VRT or a GeoTiff for an area of interest in a projection of your choice. In this example we will use `EPSG=4326`

##### To local VRT in homolosine (directly from the webdav connection)
The first step is to obtain a VRT for the area of interest in the Homolosine projection. We suggest to use VRT for the intermediate steps to save space and computation times.

``` bash
gdal_translate -of VRT -projwin $BOUNDS -tr $CELL_SIZE \
    -co "TILED=YES" -co "COMPRESS=DEFLATE" -co "PREDICTOR=2" -co "BIGTIFF=YES" \
    "/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data/ocs_0-30cm_mean.vrt" \
    "ocs_0-5cm_mean.vrt"

```

##### To a VRT in, for example, LatLong
The following command will generate a VRT in the projection of your choice:

```bash
gdalwarp -overwrite -t_srs EPSG:4326 -of VRT "ocs_0-5cm_mean.vrt" "ocs_0-5cm_mean_4326.vrt"
```

##### To a final Geotiff
The following command will generate a Geotiff in the projection of your choice for the area of interest defined above

```bash
gdal_translate ocs_0-5cm_mean_4326.vrt ocs_0-5cm_mean_4326.tif \
    -co "TILED=YES" -co "COMPRESS=DEFLATE" -co "PREDICTOR=2" -co "BIGTIFF=YES"
```

# To download a global geotiff in Homolosine projection
```bash
gdal_translate \
    -co "TILED=YES" -co "COMPRESS=DEFLATE" -co "PREDICTOR=2" -co "BIGTIFF=YES" \
    $sg_url"/ocs/ocs_0-30cm_mean.vrt" \
    "ocs_0-5cm_mean.tif"

```
