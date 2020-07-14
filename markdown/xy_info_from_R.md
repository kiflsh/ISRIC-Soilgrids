<a href="https://www.isric.org" rel="isric.org"> <img src="https://www.isric.org/themes/custom/basic/logo.svg"  height="130"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="https://soilgrids.org" rel="soilgrids.org"> <img src="https://www.isric.org/sites/default/files/styles/gallery_big_image_900x700/public/SoilGrids_banner_web.png"  height="130">

# Get SoilGrids layers values for a list of points

## Load libraries

```R
library(rgdal)
library(gdalUtils)
library(sf)
```

## Set variables of interest

We define the variables for the soil property and layer of interest. See [here](https://www.isric.org/explore/soilgrids/faq-soilgrids#What_do_the_filename_codes_mean) for the naming conventions

```R
voi = "soc" # variable of interest
depth = "0-5cm"
layer = "mean"

voi_layer = paste(voi,depth,layer, sep="_") # layer of interest 
```

We set other variables necessary for the WCS call for all kinds of requests

```R
webdav_path = '/vsicurl?max_retry=3&retry_delay=1&list_dir=no&url=https://files.isric.org/soilgrids/latest/data/'
```

## Read in point data and re-project to Homolosine

```R
data=read.csv("my_points_data.csv")

spdata=st_as_sf(data,coords = c("longitude", "latitude"), crs = 4326)

igh='+proj=igh +lat_0=0 +lon_0=0 +datum=WGS84 +units=m +no_defs'
spdata_igh=st_transform(spdata, igh)

data_igh=data.frame(st_coordinates(spdata_igh),id=spdata_igh$my_id)

```

## Use gdallocationinfo to get the values from the pixels

```R
fun_pixel_values=function(rowPX,data,VOI,VOI_LYR){
    as.numeric(
        gdallocationinfo(
            srcfile=paste0(webdav_path,"/",VOI,"/", VOI_LYR),
            x=data[rowPX,"X"],
            y=data[rowPX,"Y"],
            geoloc=TRUE,
            valonly=TRUE))
}

}

value_pixels=unlist(lapply(1:3,function(x){fun_pixel_values(x,data_igh,voi,voi_layer)}))
```

The `value_pixels` vector can be binded with `data_igh` paying attention to order. [`gdallocationinfo`](https://gdal.org/programs/gdallocationinfo.html) can also return more information, including the coordinates themselves for a safer workflow.








