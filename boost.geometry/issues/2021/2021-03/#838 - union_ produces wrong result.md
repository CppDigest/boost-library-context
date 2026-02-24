# #838 - union_ produces wrong result [Closed]

> Username: amagdum  
> Created at: 2021-03-29 16:30:01 UTC  
> Updated at: 2021-04-28 22:24:55 UTC  
> Closed at: 2021-04-28 08:33:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/838  

Result of simple union of two polygon is failing when BOOST_GEOMETRY_NO_ROBUSTNESS is enabled. I have been used no robustness as using robustness causes other issues with buffer operation. This is tested with latest boost 1.75.  
  
std::string polygon1_wkt = "POLYGON((27162.5232832765 -40045.435290614478,27162.764636849268 -40045.321663646188,27164.74034168441 -40044.391828870466,27164.854765308635 -40044.634874609801,27164.613411735867 -40044.748501578091,27162.637706900725 -40045.678336353812,27162.5232832765 -40045.435290614478))";  
  
using Point2d =  
    boost::geometry::model::d2::point_xy<double>  
boost::geometry::model::polygon<Point2d> polygon1;  
  boost::geometry::read_wkt(polygon1_wkt, polygon1);   
  
  std::string polygon2_wkt = "POLYGON((27164.783474018484 -40044.371515841653,27165.010815897258 -40044.854409784071,27162.750625155277 -40045.918184556889,27162.523283276503 -40045.43529061447,27164.783474018484 -40044.371515841653))";  
boost::geometry::model::polygon<Point2d> polygon2;  
  boost::geometry::read_wkt(polygon2_wkt, polygon2);   
  
boost::geometry::model::multi_polygon<Point2d> result;  
  boost::geometry::union_(polygon1, polygon2, result);  
  std::string message;  
  std::cout<< "is result valid = " << boost::geometry::is_valid(result, message) << message << std::endl;

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-03-29 18:27:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-809610676  

Thanks for the issue I will check it next Wednesday

---

## Comment 2

> Username: amagdum  
> Created at: 2021-03-29 20:01:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-809672818  

Thanks. Also let me know if there is anyway to workaround this without waiting for next release. I know it works w/o BOOST_GEOMETRY_NO_ROBUSTNESS but I need a runtime workaround w/o having to change BOOST_GEOMETRY_NO_ROBUSTNESS globally.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-03-31 11:50:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-811003380  

I can reproduce it. The union is successful (i.e. give the right area) but is indeed invalid. I don't know yet why it is invalid.  
For the moment I don't know a workaround, apart from not checking validity.  
I added your testcase to our suite.

---

## Comment 4

> Username: amagdum  
> Created at: 2021-03-31 17:04:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-811256700  

Thanks. BTW, if you plot resulting polygon, you can see a spike.   
  
![image](https://user-images.githubusercontent.com/51491348/113183131-8abd7d80-9208-11eb-953b-a5dd83826b98.png)

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-04-02 08:44:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-812421809  

> Thanks. BTW, if you plot resulting polygon, you can see a spike.  
  
Thank you! That might give some information. I did plot the polygon (I always do when creating a testcase) but I didn't see it. I will check it again next week.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2021-04-07 12:22:04 UTC  
> Updated at: 2021-04-07 12:22:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-814870075  

>  I will check it again next week.  
  
Yes, I've the same, but it was outside the picture (because the bounding box was based on the input). This artifact makes it maybe easier to fix. To be continued.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2021-04-28 08:33:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-828263872  

The root cause is found and fixed in #842. You can now use `BOOST_GEOMETRY_NO_ROBUSTNESS` for this case (or similar cases) and it will not generate the artifact. It will be available in Boost 1.77  
Thanks for the report and I will close this issue.

---

## Comment 8

> Username: amagdum  
> Created at: 2021-04-28 22:24:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/838#issuecomment-828821165  

Good to know, thanks for the fix!
