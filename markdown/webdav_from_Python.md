<a href="https://www.isric.org" rel="isric.org"> <img src="https://www.isric.org/themes/custom/basic/logo.svg"  height="130"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="https://soilgrids.org" rel="soilgrids.org"> <img src="https://www.isric.org/sites/default/files/styles/gallery_big_image_900x700/public/SoilGrids_banner_web.png"  height="130">

# [WebDAV](https://files.isric.org/soilgrids/data/recent/): direct access to the maps in VRT format.

### Python3
#### Import packages, select boundary box and cell size

Initially you need to import the following package and select the bounding box of the area you are interested in:

```python
from osgeo import gdal,ogr,osr

bb=-337500.000,1242500.000,152500.000,527500.000 # Example bounding box (homolosine) for Ghana
igh='+proj=igh +lat_0=0 +lon_0=0 +datum=WGS84 +units=m +no_defs' # proj string for Homolosine projection
res=250 

sg_url="/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data/"
```

#### To a geotiff in Homolosine
This GDAL command will create a local geotiff in the Homolosine projection

``` python
kwargs = {'format': 'GTiff', 'projWin': bb, 'projWinSRS': igh, 'xRes': res, 'yRes': res, 'creationOptions': ["TILED=YES", "COMPRESS=DEFLATE", "PREDICTOR=2", "BIGTIFF=YES"]}

ds = gdal.Translate('./crop_roi_igh_py.tif', 
                    sg_url + 'ocs/ocs_0-30cm_mean.vrt', 
                    **kwargs)
del ds

```

#### To a geotiff in a different projection
The following commands describe a workflow to obtain a VRT or a GeoTiff for an area of interest in a projection of your choice. In this example we will use EPSG=4326

##### To local VRT in homolosine (directly from the webdav connection)
The first step is to obtain a VRT for the area of interest in the Homolosine projection. We suggest to use VRT for the intermediate steps to save space and computation times.

```python
kwargs = {'format': 'GTiff', 'projWin': bb, 'projWinSRS': igh, 'xRes': res, 'yRes': res}

ds = gdal.Translate('./crop_roi_igh_py.vrt', 
                    '/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data/ocs/ocs_0-30cm_mean.vrt', 
                    **kwargs)
del ds

```

##### To a VRT in, for example, LatLong
The following command will generate a VRT in the projection of your choice:

```python
ds = gdal.Warp('./crop_roi_ll_py.vrt', 
    './crop_roi_igh_py.vrt', 
    dstSRS='EPSG:4326')
del ds
```

##### To a final Geotiff
The following command will generate a Geotiff in the projection of your choice for the area of interest defined above

```python
kwargs = {'format': 'GTiff', 'creationOptions': ["TILED=YES", "COMPRESS=DEFLATE", "PREDICTOR=2", "BIGTIFF=YES"] }
ds = gdal.Translate('./crop_roi_ll_py.tif',
    './crop_roi_ll_py.vrt', 
    **kwargs)

del ds
```

# To download a global geotiff in Homolosine projection
``` python
kwargs = {'format': 'GTiff', 'creationOptions': ["TILED=YES", "COMPRESS=DEFLATE", "PREDICTOR=2", "BIGTIFF=YES"]}

ds = gdal.Translate('./crop_roi_igh_py.tif', 
                    '/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data/ocs/ocs_0-30cm_mean.vrt', 
                    **kwargs)
del ds

```

