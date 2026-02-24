# #656 - Invalid result of intersection for MLs, MPo -> MLs [Open]

> Username: awulkiew  
> Created at: 2020-01-07 22:57:37 UTC  
> Updated at: 2020-01-10 00:19:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/656  

Geometry develop (1.72.0), with VS2017 x64.  
  
![image](https://user-images.githubusercontent.com/1226951/71935978-78d96000-31a8-11ea-89d0-82bb5f6d8b5f.png)  
  
```  
typedef bg::model::point<double, 2, bg::cs::cartesian> point;  
typedef bg::model::linestring<point> linestring;  
typedef bg::model::polygon<point> polygon;  
typedef bg::model::multi_linestring<linestring> multi_linestring;  
typedef bg::model::multi_polygon<polygon> multi_polygon;  
  
multi_polygon mpo{ { { {0, 0}, {0, 1}, {1, 1}, {1, 0}, {0, 0} },  
                     { {0, 0}, {0.2, 0.1}, {0.1, 0.2}, {0, 0}  } } };  
multi_linestring mls{ { {0, 0}, {1, 0}, {1, 1}, {2, 0}, {3, 0} },  
                      { {-1, -1}, {0, 0}, {0, -1} } };  
multi_linestring res_mls;  
  
bg::intersection(mls, mpo, res_mls);  
```  
  
Result is: `MULTILINESTRING((0 0, 1 0, 1 1), (1.249e-16 2.498e-16, 0 0, 0 -1))`  
should be: `MULTILINESTRING((0 0, 1 0, 1 1))`
