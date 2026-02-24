# #1014 Add missing projections and fixing existing ones [Merged]

> Username: vissarion  
> Created at: 2022-06-07 12:00:50 UTC  
> Updated at: 2022-06-08 09:43:26 UTC  
> Merged at: 2022-06-08 09:43:19 UTC  
> Closed at: 2022-06-08 09:43:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1014  

## Features  
  
This PR adds missing projections and fixes existing ones. Affected projections are:   
|EPSG | NAME|  
| --- | ----------- |  
|1052|	Colombia Urban|  
|9807|	Transverse Mercator|  
|9808|	Transverse Mercator (South Orientated)|  
|9812|	Hotine Oblique Mercator (variant A)|  
|9819|	Krovak|  
|9830|	Polar Stereographic (variant C)|  
|9833|       Hyperbolic Cassini-Soldner|  
  
The PR also adds (partial) support for axis orientation using the +axis proj4 argument.   
  
## Tests  
  
The table below illustrates the examples from IOPG Geomatics - Guidance Note Number 7, part 2 - Coordinate Conversions and Transformations including Formulas - Revised - September 2019 and the results obtained for those examples using   
 -`proj4` with WGS84 as a source SRS   
- `proj4` with WGS84 as a source SRS with `to_wgs84=0,0,0`  
- `proj9` using EPSG codes   
- boost geometry with WGS84 as a source SRS  
  
Boost Geometry returns the expected results (as described in IOPG publication) expect from the following cases:  
  
- EPSG 1028 Equidistant Cylindrical: the implemented formula (in proj system as well as in BG) is the spherical and not the ellispoidal described in IOPG  
- EPSG 9803 Lambert Conic Conformal (2SP Belgium): the Belgium variation is not implemented in proj4 but our implementation matches the proj9 latest computation  
  
where BG returns the same results as `proj4`.  
  
The following methods are not implemented in any proj system nor BG.   
- 1042	Krovak Modified                              
- 1043	Krovak Modified (North Orientated)           
- 9816	Tunisia Mining Grid                          
- 9826	Lambert Conic Conformal (West Orientated)  
  
|      |                                            |             | in             |                | out proj4    |              | out proj4 with +wgs84=0 on source |                    | out proj9 with EPSG codes             |             | IOGP Publication |                  |  
| ---- | ------------------------------------------ | ----------- | -------------- | -------------- | ------------ | ------------ | ------------------ | ------------------ | -------------------------- | ----------- | ---------------- | ---------------- |  
| EPSG | METHOD\_NAME                               | EXAMPLE SRS | lon            | lat            | x            | y            | x                  | y                  | x                          | y           | x                | y                |  
| 1024 | Popular Visualisation Pseudo Mercator      | 3857        |                |                |              |              |                    |                    |                            |             |                  |                  |  
| 1027 | Lambert Azimuthal Equal Area (Spherical)   | 2163        | 10             | 52             | 4392386.64   | 5360932.79   | 4413901.78         | 5358732.97         | 4392386.64                 | 5360932.79  | no\_example      | no\_example      |  
| 1028 | Equidistant Cylindrical                    | 4087        | 10             | 55             | 1113194.91   | 6122571.99   | 1113194.91         | 6122571.99         | 1113194.91                 | 6122571.99  | 1113194.91       | 6097230.31       |  
| 1029 | Equidistant Cylindrical (Spherical)        | 4088        | 10             | 52             | 1111950.49   | 5782142.54   | 1111950.48817606   | 5761364.71140026   | 1111950.49                 | 5782142.54  | no\_example      | no\_example      |  
| 1041 | Krovak (North Orientated)                  | 5514        | 16.84977       | 50.20901       | \-568885.63  | \-1050469.45 | \-568885.630165686 | \-1050469.44564621 | \-568880.26                | \-1050470.5 | \-568991         | \-1050538.64     |  
| 1042 | Krovak Modified                            | 5515        |        not implemented        |                |              |              |                    |                    |                            |             |                  |                  |  
| 1043 | Krovak Modified (North Orientated)         | 5516        |       not implemented         |                |              |              |                    |                    |                            |             |                  |                  |  
| 1051 | Lambert Conic Conformal (2SP Michigan)     | 6201        | \-83.166666653 | 43.750000014   | 2308333.8    | 156159.74    | 2308333.8          | 156159.74          | 2308299.36                 | 160209.97   | 2308335.75       | 160210.48        |  
| 1052 | Colombia Urban                             | 6247        | \-74.250000023 | 4.7999999945   | 80859.03     | 122543.17    | 80859.03           | 122543.17          | 122543.17                  | 80859.03    | 80859.03         | 122543.17        |  
| 9801 | Lambert Conic Conformal (1SP)              | 24200       | \-76.943683174 | 17.932166647   | 255966.6     | 142493.51    | 255854.11          | 142204.1           | 255854.11                  | 142204.1    | 255966.58        | 142493.51        |  
| 9802 | Lambert Conic Conformal (2SP)              | 32040       | \-95.99999989  | 28.500000182   | 2963503.95   | 254759.87    | 2963584.8          | 254662             | 2963584.8                  | 254662      | 2963503.91       | 254759.8         |  
| 9803 | Lambert Conic Conformal (2SP Belgium)      | 31300       | 5.80737015     | 50.679572292   | 252508.5     | 153048.62    | 252415.17          | 153108.9           | 252415.16                  | 153108.84   | 251763.2         | 153034.13        |  
| 9804 | Mercator (variant A)                       | 3002        | 119.99999986   | \-3.0000001398 | 5009726.57   | 569150.8     | 5009477.8          | 568973.52          | 5009477.8                  | 568973.52   | 5009726.58       | 569150.82        |  
| 9805 | Mercator (variant B)                       | 3388        | 52.999999796   | 52.999999796   | 165704.28    | 5171848.04   | 165822.37          | 5171815.93         | 165822.48                  | 5171815.15  | 165704.29        | 5171848.07       |  
| 9806 | Cassini-Soldner                            | 30200       | \-62.000000216 | 10.000000275   | 66644.82     | 82536.37     | 66247.56           | 80477.43           | 66247.56                   | 80477.43    | 66644.94         | 82536.22         |  
| 9807 | Transverse Mercator                        | 27700       | 0.50000021429  | 50.499999871   | 577275       | 69740.48     | 577393.39          | 69673.61           | 577393.39                  | 69673.61    | 577274.99        | 69740.5          |  
| 9808 | Transverse Mercator (South Orientated)     | 2053        | 28.282632944   | \-25.732028354 | 71984.49     | 2847342.74   | 71984.49           | 2847342.74         | 71984.49                   | 2847342.74  | 71984.49         | 2847342.74       |  
| 9809 | Oblique Stereographic                      | 28992       | 5.9999999931   | 53.000000025   | 196105.28    | 557057.74    | 196139.44          | 557179.1           | 196139.44                  | 557179.1    | 196105.283       | 557057.739       |  
| 9810 | Polar Stereographic (variant A)            | 5041        | 44.000000007   | 73.000000003   | 3320416.75   | 632668.43    | 3320416.75         | 632668.43          | 3320416.75                 | 632668.43   | 3320416.75       | 632668.43        |  
| 9811 | New Zealand Map Grid                       | 27200       | 174.763336     | \-36.848461    | 2667669.23   | 6482384.58   | 2667648.04         | 6482184.84         | 2667648.04                 | 6482184.84  | no\_example      | no\_example      |  
| 9812 | Hotine Oblique Mercator (variant A)        | 3079        | 117            | 12             | \-4893794.43 | 12634528.93  | \-4893794.43       | 12634528.93        | \-4893794.7                | 12634529.87 | no\_example      | no\_example      |  
| 9813 | Laborde Oblique Mercator                   | 8441        | 44.45757       | \-16.189799986 | 188333.59    | 1100071.08   | 188364.97          | 1100212.76         | 188365                     | 1098982.77  | 188333.848       | 1098841.091      |  
| 9815 | Hotine Oblique Mercator (variant B)        | 29873       | 115.80550545   | 5.3872536023   | 679245.73    | 596562.78    | 678925.28          | 596659.27          | 678925.28                  | 596659.27   | 679245.73        | 596562.78        |  
| 9816 | Tunisia Mining Grid                        | 22300       |        not implemented        |                |              |              |                    |                    |                            |             |                  |                  |  
| 9817 | Lambert Conic Near-Conformal               | 22700       | 34.136469742   | 37.521562493   | 15708        | 623167.19    | 15595.79           | 623225.84          | inf                        |             | 15707.96         | 623165.96        |  
| 9818 | American Polyconic                         | 5880        | \-45           | \-6            | 5996378.71   | 9328349.94   | 5996378.71         | 9328349.94         | 5996378.71                 | 9328349.94  | 5996378.71       | 9328349.944      |  
| 9819 | Krovak                                     | 5513        | 16.84977       | 50.20901       | 1050536.26   | 568991.16    | 1050469.45         | 568885.63          | 1050470.5                  | 568880.26   | 1050538.64       | 568991           |  
| 9820 | Lambert Azimuthal Equal Area               | 3035        | 5              | 50             | 3962799.45   | 2999718.85   | 3962799.45         | 2999718.85         | 3962799.45                 | 2999718.85  | 3962799.45       | 2999718.85       |  
| 9822 | Albers Equal Area                          | 3174        | \-78.75        | 42.749999987   | 1466493.49   | 702903       | 1466493.49         | 702903             | 1466493.49                 | 702903      | 1466493.49       | 702903           |  
| 9824 | Transverse Mercator Zoned Grid System      | 32600       | 12             | 56             | 1798179.04   | 13588963.31  | 1798179.04         | 13588963.31        | \* inf                     | \* inf      | no\_example      | no\_example      |  
| 9824 |                                            | 32700       | 174            | \-44           | \-2617060.16 | 4328084.49   |                    |                    |                            |             | no\_example      | no\_example      |  
| 9826 | Lambert Conic Conformal (West Orientated)  | 2218        |       not implemented         |                |              |              |                    |                    |                            |             |                  |                  |  
| 9828 | Bonne (South Orientated)                   | 5017        | \-9.142685     | 38.736946      | 87766.67     | \-3183066.77 | 87766.67           | \-3183066.77       | error No inverse operation |             | no\_example      | no\_example      |  
| 9829 | Polar Stereographic (variant B)            | 3032        | 120            | \-75           | 7255380.79   | 7053389.56   | 7255380.79         | 7053389.56         | 7255380.79                 | 7053389.56  | 7255380.79       | 7053389.56       |  
| 9830 | Polar Stereographic (variant C)            | 2985        | 140.07140001   | \-66.605227791 | 303169.52    | 2743419.33   | 303169.52          | 2743428.81         | \* inf                     | \* inf      | 303169.52        | 244055.72        |  
| 9831 | Guam Projection                            | 3993        | 144.63533131   | 13.33903845    | 37712.48     | 35242        | 37452.29           | 35082.3            | 37452.29                   | 35082.3     | 37712.48         | 35242            |  
| 9832 | Modified Azimuthal Equidistant             | 3295        | 138.19303      | 9.5965258594   | 42665.91     | 65509.83     | 42414.3977595323   | 65317.2630414931   | 42665.91                   | 65509.83    | 42665.91         | 65509.83         |  
| 9833 | Hyperbolic Cassini-Soldner                 | 3139        | 179.99433651   | \-16.841456514 | 1595642.29   | 1343096.93   | 1597583.63         | 1342373.87         | 1603470.19                 | 1336242.84  | 16015.289        | 13369.6601       |  
| 9834 | Lambert Cylindrical Equal Area (Spherical) | 3410        | 10             | 52             | 963010.77    | 5797285.11   | 963010.77          | 5782482.74         | 963010.77                  | 5797285.11  | 963010.77        | 5797285.11       |  
| 9835 | Lambert Cylindrical Equal Area             | 6933        | 10             | 52             | 964862.8     | 5775916.83   | 964862.8           | 5775916.83         | 964862.8                   | 5775916.83  | 964862.802508964 | 5775916.83074435 |

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-07 12:12:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1014#pullrequestreview-998028610  

📁 include/boost/geometry/srs/projections/proj/omerc.hpp

```diff
  88 |-                         W = this->m_proj_parm.E / math::pow(pj_tsfn(lp_lat, sin(lp_lat), par.e), this->m_proj_parm.B);
  90 |+                         W = this->m_proj_parm.E / math::pow(pj_tsfn(lp_lat, sin(lp_lat), par.e),
  91 |+                             this->m_proj_parm.B);
```

> Username: awulkiew  
> Created_at: 2022-06-07 12:12:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1014#discussion_r891140397  

This alignment looks confusing because this is not the rest of the expression but a parameter of `pow`. I'd align it with `pj_tsfn` line above. What do you think?

> Username: vissarion  
> Created_at: 2022-06-07 12:16:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1014#discussion_r891143876  

I agree.

> Username: awulkiew  
> Created_at: 2022-06-07 12:16:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1014#discussion_r891143887  

However if this was taken from the original proj then I'd leave it as it is.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-07 12:18:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1014#pullrequestreview-998035468  

📁 include/boost/geometry/srs/projections/proj/omerc.hpp

```diff
 102 |-                             u = this->m_proj_parm.ArB * atan2((s * this->m_proj_parm.cosgam + V * this->m_proj_parm.singam), temp);
 105 |+                             u = this->m_proj_parm.ArB * atan2((s * this->m_proj_parm.cosgam
 106 |+                                 + V * this->m_proj_parm.singam), temp);
```

> Username: awulkiew  
> Created_at: 2022-06-07 12:18:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1014#discussion_r891145273  

Here is another place. There are also other places below. I won't point them all out. The comment I made above about the original proj code applies here and below too.


---

## Review 3 [Approved]

> Username: awulkiew  
> Created_at: 2022-06-07 12:20:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1014#pullrequestreview-998038540  

Thanks! LGTM

---

## Review 4 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-08 09:34:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1014#pullrequestreview-999420567  

📁 include/boost/geometry/srs/projections/impl/pj_generic_inverse.hpp

```diff
  64 |+                            Projection const& proj,
  65 |+                            T& lp_lat,
  66 |+                            T& lp_lon)
```

> Username: barendgehrels  
> Created_at: 2022-06-08 09:33:48 UTC  
> Updated_at: 2022-06-08 09:34:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1014#discussion_r892139107  

Cool, new projection.  
  
I cannot oversee all the details of course. Thanks for the work!


---
