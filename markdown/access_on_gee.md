<a href="https://www.isric.org" rel="isric.org"> <img src="https://www.isric.org/themes/custom/basic/logo.svg"  height="130"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="https://soilgrids.org" rel="soilgrids.org"> <img src="https://www.isric.org/sites/default/files/styles/gallery_big_image_900x700/public/SoilGrids_banner_web.png"  height="130">

# SoilGrids on Google Earth Engine (GEE)

SoilGrids250m 2.0 data are available on GEE as as Community contributed Datasets. Currently, layers representing the mean of predictive distribution are served. The layers quantifying the median, the 5th and the 95th percentiles of the distributions will be uploaded shortly.

Each asset is an image with 6 bands, a band for each depth (0-5cm, 5-15cm,15-30cm,30-60cm,60-100cm,100-200cm).

### Updated list of available layers as GEE assets :


| Acronym | Property | Assets on GEE |
|------ | ------ | ------ |
|bdod | Bulk density | [bdod_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/bdod_mean) | 
|cec | Cation exchange capacity at pH7 | [cec_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/cec_mean) |
|cfvo   | Coarse fragments | [cfvo_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/cfvo_mean) | 
|clay | Clay | [clay_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/clay_mean) |
|nitrogen | Total Nitrogen | [nitrogen_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/nitrogen_mean) |
|ocd | Organic carbon density | [ocd_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/ocd_mean) |
|ocs | Organic carbon stock | [ocs_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/ocs_mean) |
|phh2o | pH in H2O | [phh2o_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/phh2o_mean) |
|sand | Sand | [sand_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/sand_mean) |
|silt | Silt | [silt_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/silt_mean) |
|soc | Soil organic carbon | [soc_mean](https://code.earthengine.google.com/?asset=projects/soilgrids-isric/soc_mean) |


### Import assets in GEE

1. Javascript

```js
var image = ee.Image("projects/soilgrids-isric/layer_name");
```

2. python

```python
image = ee.Image("projects/soilgrids-isric/layer_name");
```

### Example script in Google Earth Engine code editor

https://code.earthengine.google.com/9e11d149a1d28d10619fa5353b895ed2
