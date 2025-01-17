---
title: "Spatial manipulations"
output: 
  html_document: 
    theme: yeti
---



[<i class="fa fa-file-code-o fa-3x" aria-hidden="true"></i> The R Script associated with this page is available here](03_manip.R).  Download this file and open it (or copy-paste into a new script) with RStudio if you want to follow along.  

# Vector manipulations
  
As mentioned in the [lecture](slides.pdf), vector files can be manipulated in a number of way with regards to their geometry. Examples include for instance combining, dissolving or splitting geometries.
A full list of methods can be queried via `methods(class = "sf")` and looked up via `?sf_combine` and similar


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(sf)
```

```
## Linking to GEOS 3.8.0, GDAL 3.0.4, PROJ 7.0.0
```

```r
# Load the example shapefile provided as part of the sf package
nc = st_read(system.file("shape/nc.shp", package="sf"))
```

```
## Reading layer `nc' from data source `/home/martin/R/x86_64-pc-linux-gnu-library/3.6/sf/shape/nc.shp' using driver `ESRI Shapefile'
## Simple feature collection with 100 features and 14 fields
## geometry type:  MULTIPOLYGON
## dimension:      XY
## bbox:           xmin: -84.32385 ymin: 33.88199 xmax: -75.45698 ymax: 36.58965
## geographic CRS: NAD27
```

```r
# 100 Features in here
# nrow(nc)

# Combine all features
nc1 <- nc %>% st_union()

# Get boundary of the unified polygon
nc2 <- nc1 %>% st_boundary()

# Simplify the boundaries
nc3 <- st_simplify(nc2,dTolerance = 0.1)
```

```
## Warning in st_simplify.sfc(nc2, dTolerance = 0.1): st_simplify does not
## correctly simplify longitude/latitude data, dTolerance needs to be in decimal
## degrees
```

```r
#  Calculate centroids of each polygon and transform them to a pseudo-mercator
nc4 <- nc %>% 
  st_centroid() %>% 
  st_transform("+init=epsg:3857")
```

```
## Warning in st_centroid.sf(.): st_centroid assumes attributes are constant over
## geometries of x
```

```
## Warning in st_centroid.sfc(st_geometry(x), of_largest_polygon =
## of_largest_polygon): st_centroid does not give correct centroids for longitude/
## latitude data
```

```
## Warning in CPL_crs_from_input(x): GDAL Message 1: +init=epsg:XXXX syntax is
## deprecated. It might return a CRS with a non-EPSG compliant axis order.
```

```r
# Finally buffer each point by ~1000m
# NOTE: For this to work correctly the projection should be meter based!
nc5 <- st_buffer(nc4, 1000)

# Note how attributes are maintained
nc5
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["AREA"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["PERIMETER"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["CNTY_"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["CNTY_ID"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["NAME"],"name":[5],"type":["fctr"],"align":["left"]},{"label":["FIPS"],"name":[6],"type":["fctr"],"align":["left"]},{"label":["FIPSNO"],"name":[7],"type":["dbl"],"align":["right"]},{"label":["CRESS_ID"],"name":[8],"type":["int"],"align":["right"]},{"label":["BIR74"],"name":[9],"type":["dbl"],"align":["right"]},{"label":["SID74"],"name":[10],"type":["dbl"],"align":["right"]},{"label":["NWBIR74"],"name":[11],"type":["dbl"],"align":["right"]},{"label":["BIR79"],"name":[12],"type":["dbl"],"align":["right"]},{"label":["SID79"],"name":[13],"type":["dbl"],"align":["right"]},{"label":["NWBIR79"],"name":[14],"type":["dbl"],"align":["right"]},{"label":["geometry"],"name":[15],"type":["S3: sfc_POLYGON"],"align":["right"]}],"data":[{"1":"0.114","2":"1.442","3":"1825","4":"1825","5":"Ashe","6":"37009","7":"37009","8":"5","9":"1091","10":"1","11":"10","12":"1364","13":"0","14":"19","15":"<S3: sfc_POLYGON>","_rn_":"1"},{"1":"0.061","2":"1.231","3":"1827","4":"1827","5":"Alleghany","6":"37005","7":"37005","8":"3","9":"487","10":"0","11":"10","12":"542","13":"3","14":"12","15":"<S3: sfc_POLYGON>","_rn_":"2"},{"1":"0.143","2":"1.630","3":"1828","4":"1828","5":"Surry","6":"37171","7":"37171","8":"86","9":"3188","10":"5","11":"208","12":"3616","13":"6","14":"260","15":"<S3: sfc_POLYGON>","_rn_":"3"},{"1":"0.070","2":"2.968","3":"1831","4":"1831","5":"Currituck","6":"37053","7":"37053","8":"27","9":"508","10":"1","11":"123","12":"830","13":"2","14":"145","15":"<S3: sfc_POLYGON>","_rn_":"4"},{"1":"0.153","2":"2.206","3":"1832","4":"1832","5":"Northampton","6":"37131","7":"37131","8":"66","9":"1421","10":"9","11":"1066","12":"1606","13":"3","14":"1197","15":"<S3: sfc_POLYGON>","_rn_":"5"},{"1":"0.097","2":"1.670","3":"1833","4":"1833","5":"Hertford","6":"37091","7":"37091","8":"46","9":"1452","10":"7","11":"954","12":"1838","13":"5","14":"1237","15":"<S3: sfc_POLYGON>","_rn_":"6"},{"1":"0.062","2":"1.547","3":"1834","4":"1834","5":"Camden","6":"37029","7":"37029","8":"15","9":"286","10":"0","11":"115","12":"350","13":"2","14":"139","15":"<S3: sfc_POLYGON>","_rn_":"7"},{"1":"0.091","2":"1.284","3":"1835","4":"1835","5":"Gates","6":"37073","7":"37073","8":"37","9":"420","10":"0","11":"254","12":"594","13":"2","14":"371","15":"<S3: sfc_POLYGON>","_rn_":"8"},{"1":"0.118","2":"1.421","3":"1836","4":"1836","5":"Warren","6":"37185","7":"37185","8":"93","9":"968","10":"4","11":"748","12":"1190","13":"2","14":"844","15":"<S3: sfc_POLYGON>","_rn_":"9"},{"1":"0.124","2":"1.428","3":"1837","4":"1837","5":"Stokes","6":"37169","7":"37169","8":"85","9":"1612","10":"1","11":"160","12":"2038","13":"5","14":"176","15":"<S3: sfc_POLYGON>","_rn_":"10"},{"1":"0.114","2":"1.352","3":"1838","4":"1838","5":"Caswell","6":"37033","7":"37033","8":"17","9":"1035","10":"2","11":"550","12":"1253","13":"2","14":"597","15":"<S3: sfc_POLYGON>","_rn_":"11"},{"1":"0.153","2":"1.616","3":"1839","4":"1839","5":"Rockingham","6":"37157","7":"37157","8":"79","9":"4449","10":"16","11":"1243","12":"5386","13":"5","14":"1369","15":"<S3: sfc_POLYGON>","_rn_":"12"},{"1":"0.143","2":"1.663","3":"1840","4":"1840","5":"Granville","6":"37077","7":"37077","8":"39","9":"1671","10":"4","11":"930","12":"2074","13":"4","14":"1058","15":"<S3: sfc_POLYGON>","_rn_":"13"},{"1":"0.109","2":"1.325","3":"1841","4":"1841","5":"Person","6":"37145","7":"37145","8":"73","9":"1556","10":"4","11":"613","12":"1790","13":"4","14":"650","15":"<S3: sfc_POLYGON>","_rn_":"14"},{"1":"0.072","2":"1.085","3":"1842","4":"1842","5":"Vance","6":"37181","7":"37181","8":"91","9":"2180","10":"4","11":"1179","12":"2753","13":"6","14":"1492","15":"<S3: sfc_POLYGON>","_rn_":"15"},{"1":"0.190","2":"2.204","3":"1846","4":"1846","5":"Halifax","6":"37083","7":"37083","8":"42","9":"3608","10":"18","11":"2365","12":"4463","13":"17","14":"2980","15":"<S3: sfc_POLYGON>","_rn_":"16"},{"1":"0.053","2":"1.171","3":"1848","4":"1848","5":"Pasquotank","6":"37139","7":"37139","8":"70","9":"1638","10":"3","11":"622","12":"2275","13":"4","14":"933","15":"<S3: sfc_POLYGON>","_rn_":"17"},{"1":"0.199","2":"1.984","3":"1874","4":"1874","5":"Wilkes","6":"37193","7":"37193","8":"97","9":"3146","10":"4","11":"200","12":"3725","13":"7","14":"222","15":"<S3: sfc_POLYGON>","_rn_":"18"},{"1":"0.081","2":"1.288","3":"1880","4":"1880","5":"Watauga","6":"37189","7":"37189","8":"95","9":"1323","10":"1","11":"17","12":"1775","13":"1","14":"33","15":"<S3: sfc_POLYGON>","_rn_":"19"},{"1":"0.063","2":"1.000","3":"1881","4":"1881","5":"Perquimans","6":"37143","7":"37143","8":"72","9":"484","10":"1","11":"230","12":"676","13":"0","14":"310","15":"<S3: sfc_POLYGON>","_rn_":"20"},{"1":"0.044","2":"1.158","3":"1887","4":"1887","5":"Chowan","6":"37041","7":"37041","8":"21","9":"751","10":"1","11":"368","12":"899","13":"1","14":"491","15":"<S3: sfc_POLYGON>","_rn_":"21"},{"1":"0.064","2":"1.213","3":"1892","4":"1892","5":"Avery","6":"37011","7":"37011","8":"6","9":"781","10":"0","11":"4","12":"977","13":"0","14":"5","15":"<S3: sfc_POLYGON>","_rn_":"22"},{"1":"0.086","2":"1.267","3":"1893","4":"1893","5":"Yadkin","6":"37197","7":"37197","8":"99","9":"1269","10":"1","11":"65","12":"1568","13":"1","14":"76","15":"<S3: sfc_POLYGON>","_rn_":"23"},{"1":"0.128","2":"1.554","3":"1897","4":"1897","5":"Franklin","6":"37069","7":"37069","8":"35","9":"1399","10":"2","11":"736","12":"1863","13":"0","14":"950","15":"<S3: sfc_POLYGON>","_rn_":"24"},{"1":"0.108","2":"1.483","3":"1900","4":"1900","5":"Forsyth","6":"37067","7":"37067","8":"34","9":"11858","10":"10","11":"3919","12":"15704","13":"18","14":"5031","15":"<S3: sfc_POLYGON>","_rn_":"25"},{"1":"0.170","2":"1.680","3":"1903","4":"1903","5":"Guilford","6":"37081","7":"37081","8":"41","9":"16184","10":"23","11":"5483","12":"20543","13":"38","14":"7089","15":"<S3: sfc_POLYGON>","_rn_":"26"},{"1":"0.111","2":"1.392","3":"1904","4":"1904","5":"Alamance","6":"37001","7":"37001","8":"1","9":"4672","10":"13","11":"1243","12":"5767","13":"11","14":"1397","15":"<S3: sfc_POLYGON>","_rn_":"27"},{"1":"0.180","2":"2.151","3":"1905","4":"1905","5":"Bertie","6":"37015","7":"37015","8":"8","9":"1324","10":"6","11":"921","12":"1616","13":"5","14":"1161","15":"<S3: sfc_POLYGON>","_rn_":"28"},{"1":"0.104","2":"1.294","3":"1907","4":"1907","5":"Orange","6":"37135","7":"37135","8":"68","9":"3164","10":"4","11":"776","12":"4478","13":"6","14":"1086","15":"<S3: sfc_POLYGON>","_rn_":"29"},{"1":"0.077","2":"1.271","3":"1908","4":"1908","5":"Durham","6":"37063","7":"37063","8":"32","9":"7970","10":"16","11":"3732","12":"10432","13":"22","14":"4948","15":"<S3: sfc_POLYGON>","_rn_":"30"},{"1":"0.142","2":"1.640","3":"1913","4":"1913","5":"Nash","6":"37127","7":"37127","8":"64","9":"4021","10":"8","11":"1851","12":"5189","13":"7","14":"2274","15":"<S3: sfc_POLYGON>","_rn_":"31"},{"1":"0.059","2":"1.319","3":"1927","4":"1927","5":"Mitchell","6":"37121","7":"37121","8":"61","9":"671","10":"0","11":"1","12":"919","13":"2","14":"4","15":"<S3: sfc_POLYGON>","_rn_":"32"},{"1":"0.131","2":"1.521","3":"1928","4":"1928","5":"Edgecombe","6":"37065","7":"37065","8":"33","9":"3657","10":"10","11":"2186","12":"4359","13":"9","14":"2696","15":"<S3: sfc_POLYGON>","_rn_":"33"},{"1":"0.122","2":"1.516","3":"1932","4":"1932","5":"Caldwell","6":"37027","7":"37027","8":"14","9":"3609","10":"6","11":"309","12":"4249","13":"9","14":"360","15":"<S3: sfc_POLYGON>","_rn_":"34"},{"1":"0.080","2":"1.307","3":"1936","4":"1936","5":"Yancey","6":"37199","7":"37199","8":"100","9":"770","10":"0","11":"12","12":"869","13":"1","14":"10","15":"<S3: sfc_POLYGON>","_rn_":"35"},{"1":"0.118","2":"1.899","3":"1937","4":"1937","5":"Martin","6":"37117","7":"37117","8":"59","9":"1549","10":"2","11":"883","12":"1849","13":"1","14":"1033","15":"<S3: sfc_POLYGON>","_rn_":"36"},{"1":"0.219","2":"2.130","3":"1938","4":"1938","5":"Wake","6":"37183","7":"37183","8":"92","9":"14484","10":"16","11":"4397","12":"20857","13":"31","14":"6221","15":"<S3: sfc_POLYGON>","_rn_":"37"},{"1":"0.118","2":"1.601","3":"1946","4":"1946","5":"Madison","6":"37115","7":"37115","8":"58","9":"765","10":"2","11":"5","12":"926","13":"2","14":"3","15":"<S3: sfc_POLYGON>","_rn_":"38"},{"1":"0.155","2":"1.781","3":"1947","4":"1947","5":"Iredell","6":"37097","7":"37097","8":"49","9":"4139","10":"4","11":"1144","12":"5400","13":"5","14":"1305","15":"<S3: sfc_POLYGON>","_rn_":"39"},{"1":"0.069","2":"1.201","3":"1948","4":"1948","5":"Davie","6":"37059","7":"37059","8":"30","9":"1207","10":"1","11":"148","12":"1438","13":"3","14":"177","15":"<S3: sfc_POLYGON>","_rn_":"40"},{"1":"0.066","2":"1.070","3":"1950","4":"1950","5":"Alexander","6":"37003","7":"37003","8":"2","9":"1333","10":"0","11":"128","12":"1683","13":"2","14":"150","15":"<S3: sfc_POLYGON>","_rn_":"41"},{"1":"0.145","2":"1.791","3":"1951","4":"1951","5":"Davidson","6":"37057","7":"37057","8":"29","9":"5509","10":"8","11":"736","12":"7143","13":"8","14":"941","15":"<S3: sfc_POLYGON>","_rn_":"42"},{"1":"0.134","2":"1.755","3":"1958","4":"1958","5":"Burke","6":"37023","7":"37023","8":"12","9":"3573","10":"5","11":"326","12":"4314","13":"15","14":"407","15":"<S3: sfc_POLYGON>","_rn_":"43"},{"1":"0.100","2":"1.331","3":"1962","4":"1962","5":"Washington","6":"37187","7":"37187","8":"94","9":"990","10":"5","11":"521","12":"1141","13":"0","14":"651","15":"<S3: sfc_POLYGON>","_rn_":"44"},{"1":"0.099","2":"1.411","3":"1963","4":"1963","5":"Tyrrell","6":"37177","7":"37177","8":"89","9":"248","10":"0","11":"116","12":"319","13":"0","14":"141","15":"<S3: sfc_POLYGON>","_rn_":"45"},{"1":"0.116","2":"1.664","3":"1964","4":"1964","5":"McDowell","6":"37111","7":"37111","8":"56","9":"1946","10":"5","11":"134","12":"2215","13":"5","14":"128","15":"<S3: sfc_POLYGON>","_rn_":"46"},{"1":"0.201","2":"1.805","3":"1968","4":"1968","5":"Randolph","6":"37151","7":"37151","8":"76","9":"4456","10":"7","11":"384","12":"5711","13":"12","14":"483","15":"<S3: sfc_POLYGON>","_rn_":"47"},{"1":"0.180","2":"2.142","3":"1973","4":"1973","5":"Chatham","6":"37037","7":"37037","8":"19","9":"1646","10":"2","11":"591","12":"2398","13":"3","14":"687","15":"<S3: sfc_POLYGON>","_rn_":"48"},{"1":"0.094","2":"1.307","3":"1979","4":"1979","5":"Wilson","6":"37195","7":"37195","8":"98","9":"3702","10":"11","11":"1827","12":"4706","13":"13","14":"2330","15":"<S3: sfc_POLYGON>","_rn_":"49"},{"1":"0.134","2":"1.590","3":"1980","4":"1980","5":"Rowan","6":"37159","7":"37159","8":"80","9":"4606","10":"3","11":"1057","12":"6427","13":"8","14":"1504","15":"<S3: sfc_POLYGON>","_rn_":"50"},{"1":"0.168","2":"1.791","3":"1984","4":"1984","5":"Pitt","6":"37147","7":"37147","8":"74","9":"5094","10":"14","11":"2620","12":"6635","13":"11","14":"3059","15":"<S3: sfc_POLYGON>","_rn_":"51"},{"1":"0.106","2":"1.444","3":"1986","4":"1986","5":"Catawba","6":"37035","7":"37035","8":"18","9":"5754","10":"5","11":"790","12":"6883","13":"21","14":"914","15":"<S3: sfc_POLYGON>","_rn_":"52"},{"1":"0.168","2":"1.995","3":"1988","4":"1988","5":"Buncombe","6":"37021","7":"37021","8":"11","9":"7515","10":"9","11":"930","12":"9956","13":"18","14":"1206","15":"<S3: sfc_POLYGON>","_rn_":"53"},{"1":"0.207","2":"1.851","3":"1989","4":"1989","5":"Johnston","6":"37101","7":"37101","8":"51","9":"3999","10":"6","11":"1165","12":"4780","13":"13","14":"1349","15":"<S3: sfc_POLYGON>","_rn_":"54"},{"1":"0.144","2":"1.690","3":"1996","4":"1996","5":"Haywood","6":"37087","7":"37087","8":"44","9":"2110","10":"2","11":"57","12":"2463","13":"8","14":"62","15":"<S3: sfc_POLYGON>","_rn_":"55"},{"1":"0.094","2":"3.640","3":"2000","4":"2000","5":"Dare","6":"37055","7":"37055","8":"28","9":"521","10":"0","11":"43","12":"1059","13":"1","14":"73","15":"<S3: sfc_POLYGON>","_rn_":"56"},{"1":"0.203","2":"3.197","3":"2004","4":"2004","5":"Beaufort","6":"37013","7":"37013","8":"7","9":"2692","10":"7","11":"1131","12":"2909","13":"4","14":"1163","15":"<S3: sfc_POLYGON>","_rn_":"57"},{"1":"0.141","2":"2.316","3":"2013","4":"2013","5":"Swain","6":"37173","7":"37173","8":"87","9":"675","10":"3","11":"281","12":"883","13":"2","14":"406","15":"<S3: sfc_POLYGON>","_rn_":"58"},{"1":"0.070","2":"1.105","3":"2016","4":"2016","5":"Greene","6":"37079","7":"37079","8":"40","9":"870","10":"4","11":"534","12":"1178","13":"4","14":"664","15":"<S3: sfc_POLYGON>","_rn_":"59"},{"1":"0.065","2":"1.093","3":"2026","4":"2026","5":"Lee","6":"37105","7":"37105","8":"53","9":"2252","10":"5","11":"736","12":"2949","13":"6","14":"905","15":"<S3: sfc_POLYGON>","_rn_":"60"},{"1":"0.146","2":"1.778","3":"2027","4":"2027","5":"Rutherford","6":"37161","7":"37161","8":"81","9":"2992","10":"12","11":"495","12":"3543","13":"8","14":"576","15":"<S3: sfc_POLYGON>","_rn_":"61"},{"1":"0.142","2":"1.655","3":"2029","4":"2029","5":"Wayne","6":"37191","7":"37191","8":"96","9":"6638","10":"18","11":"2593","12":"8227","13":"23","14":"3073","15":"<S3: sfc_POLYGON>","_rn_":"62"},{"1":"0.154","2":"1.680","3":"2030","4":"2030","5":"Harnett","6":"37085","7":"37085","8":"43","9":"3776","10":"6","11":"1051","12":"4789","13":"10","14":"1453","15":"<S3: sfc_POLYGON>","_rn_":"63"},{"1":"0.118","2":"1.506","3":"2032","4":"2032","5":"Cleveland","6":"37045","7":"37045","8":"23","9":"4866","10":"10","11":"1491","12":"5526","13":"21","14":"1729","15":"<S3: sfc_POLYGON>","_rn_":"64"},{"1":"0.078","2":"1.384","3":"2034","4":"2034","5":"Lincoln","6":"37109","7":"37109","8":"55","9":"2216","10":"8","11":"302","12":"2817","13":"7","14":"350","15":"<S3: sfc_POLYGON>","_rn_":"65"},{"1":"0.125","2":"1.601","3":"2039","4":"2039","5":"Jackson","6":"37099","7":"37099","8":"50","9":"1143","10":"2","11":"215","12":"1504","13":"5","14":"307","15":"<S3: sfc_POLYGON>","_rn_":"66"},{"1":"0.181","2":"1.980","3":"2040","4":"2040","5":"Moore","6":"37125","7":"37125","8":"63","9":"2648","10":"5","11":"844","12":"3534","13":"5","14":"1151","15":"<S3: sfc_POLYGON>","_rn_":"67"},{"1":"0.143","2":"1.887","3":"2041","4":"2041","5":"Mecklenburg","6":"37119","7":"37119","8":"60","9":"21588","10":"44","11":"8027","12":"30757","13":"35","14":"11631","15":"<S3: sfc_POLYGON>","_rn_":"68"},{"1":"0.091","2":"1.321","3":"2042","4":"2042","5":"Cabarrus","6":"37025","7":"37025","8":"13","9":"4099","10":"3","11":"856","12":"5669","13":"20","14":"1203","15":"<S3: sfc_POLYGON>","_rn_":"69"},{"1":"0.130","2":"1.732","3":"2044","4":"2044","5":"Montgomery","6":"37123","7":"37123","8":"62","9":"1258","10":"3","11":"472","12":"1598","13":"8","14":"588","15":"<S3: sfc_POLYGON>","_rn_":"70"},{"1":"0.103","2":"1.461","3":"2045","4":"2045","5":"Stanly","6":"37167","7":"37167","8":"84","9":"2356","10":"5","11":"370","12":"3039","13":"7","14":"528","15":"<S3: sfc_POLYGON>","_rn_":"71"},{"1":"0.095","2":"1.471","3":"2047","4":"2047","5":"Henderson","6":"37089","7":"37089","8":"45","9":"2574","10":"5","11":"158","12":"3679","13":"8","14":"264","15":"<S3: sfc_POLYGON>","_rn_":"72"},{"1":"0.078","2":"1.202","3":"2056","4":"2056","5":"Graham","6":"37075","7":"37075","8":"38","9":"415","10":"0","11":"40","12":"488","13":"1","14":"45","15":"<S3: sfc_POLYGON>","_rn_":"73"},{"1":"0.104","2":"1.548","3":"2065","4":"2065","5":"Lenoir","6":"37107","7":"37107","8":"54","9":"3589","10":"10","11":"1826","12":"4225","13":"14","14":"2047","15":"<S3: sfc_POLYGON>","_rn_":"74"},{"1":"0.098","2":"1.389","3":"2067","4":"2067","5":"Transylvania","6":"37175","7":"37175","8":"88","9":"1173","10":"3","11":"92","12":"1401","13":"4","14":"104","15":"<S3: sfc_POLYGON>","_rn_":"75"},{"1":"0.091","2":"1.470","3":"2068","4":"2068","5":"Gaston","6":"37071","7":"37071","8":"36","9":"9014","10":"11","11":"1523","12":"11455","13":"26","14":"2194","15":"<S3: sfc_POLYGON>","_rn_":"76"},{"1":"0.060","2":"1.036","3":"2071","4":"2071","5":"Polk","6":"37149","7":"37149","8":"75","9":"533","10":"1","11":"95","12":"673","13":"0","14":"79","15":"<S3: sfc_POLYGON>","_rn_":"77"},{"1":"0.131","2":"1.677","3":"2082","4":"2082","5":"Macon","6":"37113","7":"37113","8":"57","9":"797","10":"0","11":"9","12":"1157","13":"3","14":"22","15":"<S3: sfc_POLYGON>","_rn_":"78"},{"1":"0.241","2":"2.214","3":"2083","4":"2083","5":"Sampson","6":"37163","7":"37163","8":"82","9":"3025","10":"4","11":"1396","12":"3447","13":"4","14":"1524","15":"<S3: sfc_POLYGON>","_rn_":"79"},{"1":"0.082","2":"1.388","3":"2085","4":"2085","5":"Pamlico","6":"37137","7":"37137","8":"69","9":"542","10":"1","11":"222","12":"631","13":"1","14":"277","15":"<S3: sfc_POLYGON>","_rn_":"80"},{"1":"0.120","2":"1.686","3":"2088","4":"2088","5":"Cherokee","6":"37039","7":"37039","8":"20","9":"1027","10":"2","11":"32","12":"1173","13":"1","14":"42","15":"<S3: sfc_POLYGON>","_rn_":"81"},{"1":"0.172","2":"1.835","3":"2090","4":"2090","5":"Cumberland","6":"37051","7":"37051","8":"26","9":"20366","10":"38","11":"7043","12":"26370","13":"57","14":"10614","15":"<S3: sfc_POLYGON>","_rn_":"82"},{"1":"0.121","2":"1.978","3":"2091","4":"2091","5":"Jones","6":"37103","7":"37103","8":"52","9":"578","10":"1","11":"297","12":"650","13":"2","14":"305","15":"<S3: sfc_POLYGON>","_rn_":"83"},{"1":"0.163","2":"1.716","3":"2095","4":"2095","5":"Union","6":"37179","7":"37179","8":"90","9":"3915","10":"4","11":"1034","12":"5273","13":"9","14":"1348","15":"<S3: sfc_POLYGON>","_rn_":"84"},{"1":"0.138","2":"1.621","3":"2096","4":"2096","5":"Anson","6":"37007","7":"37007","8":"4","9":"1570","10":"15","11":"952","12":"1875","13":"4","14":"1161","15":"<S3: sfc_POLYGON>","_rn_":"85"},{"1":"0.098","2":"1.262","3":"2097","4":"2097","5":"Hoke","6":"37093","7":"37093","8":"47","9":"1494","10":"7","11":"987","12":"1706","13":"6","14":"1172","15":"<S3: sfc_POLYGON>","_rn_":"86"},{"1":"0.167","2":"2.709","3":"2099","4":"2099","5":"Hyde","6":"37095","7":"37095","8":"48","9":"338","10":"0","11":"134","12":"427","13":"0","14":"169","15":"<S3: sfc_POLYGON>","_rn_":"87"},{"1":"0.204","2":"1.871","3":"2100","4":"2100","5":"Duplin","6":"37061","7":"37061","8":"31","9":"2483","10":"4","11":"1061","12":"2777","13":"7","14":"1227","15":"<S3: sfc_POLYGON>","_rn_":"88"},{"1":"0.121","2":"1.855","3":"2107","4":"2107","5":"Richmond","6":"37153","7":"37153","8":"77","9":"2756","10":"4","11":"1043","12":"3108","13":"7","14":"1218","15":"<S3: sfc_POLYGON>","_rn_":"89"},{"1":"0.051","2":"1.096","3":"2109","4":"2109","5":"Clay","6":"37043","7":"37043","8":"22","9":"284","10":"0","11":"1","12":"419","13":"0","14":"5","15":"<S3: sfc_POLYGON>","_rn_":"90"},{"1":"0.177","2":"2.916","3":"2119","4":"2119","5":"Craven","6":"37049","7":"37049","8":"25","9":"5868","10":"13","11":"1744","12":"7595","13":"18","14":"2342","15":"<S3: sfc_POLYGON>","_rn_":"91"},{"1":"0.080","2":"1.188","3":"2123","4":"2123","5":"Scotland","6":"37165","7":"37165","8":"83","9":"2255","10":"8","11":"1206","12":"2617","13":"16","14":"1436","15":"<S3: sfc_POLYGON>","_rn_":"92"},{"1":"0.195","2":"1.783","3":"2146","4":"2146","5":"Onslow","6":"37133","7":"37133","8":"67","9":"11158","10":"29","11":"2217","12":"14655","13":"23","14":"3568","15":"<S3: sfc_POLYGON>","_rn_":"93"},{"1":"0.240","2":"2.004","3":"2150","4":"2150","5":"Robeson","6":"37155","7":"37155","8":"78","9":"7889","10":"31","11":"5904","12":"9087","13":"26","14":"6899","15":"<S3: sfc_POLYGON>","_rn_":"94"},{"1":"0.125","2":"2.868","3":"2156","4":"2156","5":"Carteret","6":"37031","7":"37031","8":"16","9":"2414","10":"5","11":"341","12":"3339","13":"4","14":"487","15":"<S3: sfc_POLYGON>","_rn_":"95"},{"1":"0.225","2":"2.107","3":"2162","4":"2162","5":"Bladen","6":"37017","7":"37017","8":"9","9":"1782","10":"8","11":"818","12":"2052","13":"5","14":"1023","15":"<S3: sfc_POLYGON>","_rn_":"96"},{"1":"0.214","2":"2.152","3":"2185","4":"2185","5":"Pender","6":"37141","7":"37141","8":"71","9":"1228","10":"4","11":"580","12":"1602","13":"3","14":"763","15":"<S3: sfc_POLYGON>","_rn_":"97"},{"1":"0.240","2":"2.365","3":"2232","4":"2232","5":"Columbus","6":"37047","7":"37047","8":"24","9":"3350","10":"15","11":"1431","12":"4144","13":"17","14":"1832","15":"<S3: sfc_POLYGON>","_rn_":"98"},{"1":"0.042","2":"0.999","3":"2238","4":"2238","5":"New Hanover","6":"37129","7":"37129","8":"65","9":"5526","10":"12","11":"1633","12":"6917","13":"9","14":"2100","15":"<S3: sfc_POLYGON>","_rn_":"99"},{"1":"0.212","2":"2.024","3":"2241","4":"2241","5":"Brunswick","6":"37019","7":"37019","8":"10","9":"2181","10":"5","11":"659","12":"2655","13":"6","14":"841","15":"<S3: sfc_POLYGON>","_rn_":"100"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

```r
# Try and plot some of the created new objects above
```


It is also easily possible to create new datasets based on existing geometries.


```r
# Make a grid overall features
x1 <- st_make_grid(nc, n = c(25,25))
```

```
## although coordinates are longitude/latitude, st_relate_pattern assumes that they are planar
```

```r
# Sample 100 points with the grid
x2 <- st_sample(x1, 100)
```

```
## although coordinates are longitude/latitude, st_intersects assumes that they are planar
```

```
## although coordinates are longitude/latitude, st_intersects assumes that they are planar
```

```r
plot(x1)
plot(x2,add=TRUE)
```

![](03_manip_files/figure-html/unnamed-chunk-3-1.png)<!-- -->


<div class="well">
Take the laxenburg polygon from the [vector](01_vector.html) session, project and buffer the polygon by 10.000m (10km).

<button data-toggle="collapse" class="btn btn-primary btn-sm round" data-target="#demo1">Show Solution</button>
<div id="demo1" class="collapse">

```r
# Get the data
laxpol <- st_read('Laxenburg.gpkg') %>% 
  st_transform("+init=epsg:3857") %>% 
  st_buffer(10000)

plot(laxpol)
```

</div>
</div>

Here we use mainly the ´sf´ package and its functionalities for manipulating vector data geometries. More and other tools are available in the ´rgeos´ package, which however only works with ´sp´ objects and can be quite tedious.


```r
library(rgeos)
```

```
## Loading required package: sp
```

```
## rgeos version: 0.5-3, (SVN revision 634)
##  GEOS runtime version: 3.8.0-CAPI-1.13.1 
##  Linking to sp version: 1.4-2 
##  Polygon checking: TRUE
```

```r
library(sp)
# Load the Meuse river data points
data(meuse)
coordinates(meuse) <- c("x", "y")

# Create a delaunay triangulation between all points
plot(gDelaunayTriangulation(meuse))
points(meuse)
```

![](03_manip_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

For a list of functions provided by the geos library, call `lsf.str("package:rgeos",pattern = 'g')` and pay attention to functions starting with 'g*'.


# Raster manipulations

Raster data can manipulated in a number of ways, which is often necessary for subsequent analyses. For instance when several input layers differ in spatial extent, resolution or projection from another.

Here are some examples how to manipulate raster data directly.


```r
library(raster)
```

```
## 
## Attaching package: 'raster'
```

```
## The following object is masked from 'package:dplyr':
## 
##     select
```

```r
ras <- raster('ht_004_clipped.tif',band = 1)
ras <- setMinMax(ras)

# Aggregate the raster by a factor of 5 (too 5000m resolution)
forest <- aggregate(ras, fact = 5)

# Get all values with 95% forest cover
forest[forest <= 950] <- NA
# And those above as
forest[forest >= 950] <- 1

# Get the cells bordering those forest grid cells
forest <- boundaries(forest,type = 'outer')

plot(forest)
```

![](03_manip_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

<div class="well">
Can you calculate the area in km² of grid cells with more than 95% forest area?

<button data-toggle="collapse" class="btn btn-primary btn-sm round" data-target="#demo2">Show Solution</button>
<div id="demo2" class="collapse">

```r
# Get all values with 95% forest cover
ras[ras <= 950] <- NA
# And those above as
ras[ras >= 950] <- 1

# Calculate the area in km2
ar <- area(ras)

# Multiple the result with the forest area
ar <- ras * ar

# The area in km2
cellStats(ar,'sum')
```

</div>
</div>

## Applying functions to raster stacks

One functionality that is commonly needed is to apply arithmetric calculations or custom functions to stacks of raster images.


```r
library(raster)

# Lets load the raster stack
ras <- stack('ht_004_clipped.tif')

# Assume we want to log10 transform all layers
ras <- calc(ras, fun = log10)

# Instead of a base R function, it is also possible to supply a custom R function.
myfunc <- function(x){ abs( x[2] - x[1] ) } # Calculate the absolute difference of the layers 2 and 1
ras <- calc(ras, fun = myfunc)

# Note that the result is not a rasterStack object anymore, but instead a single rasterLayer
plot(ras, col = rainbow(10))
```

![](03_manip_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Note that this is run on a single core. To run this code in parallel, have a look at the `mclapply` function from the `parallel` package.

