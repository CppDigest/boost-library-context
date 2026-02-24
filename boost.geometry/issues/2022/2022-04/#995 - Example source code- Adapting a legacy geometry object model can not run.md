# #995 - Example source code: Adapting a legacy geometry object model can not run [Open]

> Username: zhengpw  
> Created at: 2022-04-27 03:48:02 UTC  
> Updated at: 2022-06-08 09:04:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/995  

https://www.boost.org/doc/libs/1_78_0/libs/geometry/doc/html/geometry/examples/example_source_code__adapting_a_legacy_geometry_object_model.html  
Actually this is not a formal code ,and is far from run, author only illustrate ideals roughly , we tried it and found still many work need to be done. Can you please finish it and upload to the same folder as c06_custom_polygon_example.cpp.

---

## Comment 1

> Username: zhengpw  
> Created at: 2022-04-28 10:22:27 UTC  
> Updated at: 2022-04-29 06:03:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/995#issuecomment-1112037312  

```cpp  
typedef RingIteratorImpl<std::vector<QLineString*>::iterator, QPoint> RingIterator;  
inline RingIterator range_begin(QRing& r)  
```  
  
This  means the iterateor of QRing returs a  QPoint, but a logical ambigious appears when you try to consider push_back QPoints into a QRing, for example when you finish the intersetion of 2 poygon result in 1 polygon, the interiors of result consists 1 QRing which consists 100 points, then you push_back them to form a Ring , then the problem comes: this Ring consist how many QLineString and each QLineString consist how many QPoints, in other words , how and when you decide the QRing split to 2, to 3  and more QlineString, 100 points=20lingstring*5Point or = 5lingstring*20Point? So ,push_back QPoints into Ring can't be defined.

---

## Comment 2

> Username: zhengpw  
> Created at: 2022-04-28 11:50:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/995#issuecomment-1112110079  

range_end(QRing& r) should also set RingIterator.pointIndex_ = (*(r.lines.end()))->points.size()
