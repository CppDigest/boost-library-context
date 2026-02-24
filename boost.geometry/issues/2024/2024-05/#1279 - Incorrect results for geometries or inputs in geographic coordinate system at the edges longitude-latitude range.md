# #1279 - Incorrect results for geometries or inputs in geographic coordinate system at the edges longitude-latitude range [Closed]

> Username: rorviksam  
> Created at: 2024-05-07 13:38:45 UTC  
> Updated at: 2024-07-24 13:44:43 UTC  
> Closed at: 2024-07-24 13:44:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279  

@vissarion @barendgehrels This is a continuation of issue #1161 which was only partially fixed.   
The code below fails if the geometry contains latitude -90 or 90. Tested on boost 1.85. It will work if you replace -90 by -89.9 or 90 by 89.9.  
```c++  
    namespace bg = boost::geometry;  
    namespace bgs = boost::geometry::strategy;  
    using std::string_literals::operator""s;  
    using Point = bg::model::point<double, 2, bg::cs::geographic<bg::degree>>;  
    using Polygon = bg::model::polygon<Point, true>;  
    Point pt_in_melbourne, pt_in_oak;  
    Polygon melbourne, oak;  
    bg::read_wkt(  
        "POLYGON ((75 -90, 75 -6, 78 -2, 92 -2, 107 -12, 114.5 -12, 115.14222222222223 "  
        "-14.136944444444444, 126.05888888888889 -23.396944444444443, 131.84 -21.2025, "  
        "136.32888888888888 -21.499722222222225, 138.39 -26.225277777777777, 146.53333333333333 "  
        "-29, 151.970694083691 -33.4304025266695, 152.93055555555554 -35.31638888888889, "  
        "150.32055555555556 -38.18861111111111, 150 -45, 163 -45, 163 -90, 75 -90))"s, melbourne);  
    bg::read_wkt("POINT(119 -46.0250000000001)"s, pt_in_melbourne);  
    bool const covers_melbourne = bg::covered_by(pt_in_melbourne, melbourne,  
                                       bgs::within::geographic_winding<bgs::vincenty>());  
    EXPECT_TRUE(covers_melbourne); // FAILS  
  
    bg::read_wkt(  
        "POLYGON ((-180 49.6783, -180 -5, -155 -5, -145 3.5, -120 3.5, -120 30, -124.2 36, "  
        "-126.93333333333334 36.46194444444445, -126.5 45, -128 48.333333333333336, -135 52.71666666666667, "  
        "-151.75 56.76166666666666, -167.81666666666666 51.4, -180 49.6783))"s,  
        oak);  
    bg::read_wkt("POINT(-150 25.9249999999999)"s, pt_in_oak);  
    bool const covers_oak = bg::covered_by(pt_in_oak, oak,  
                                       bgs::within::geographic_winding<bgs::vincenty>());  
    EXPECT_TRUE(covers_oak); // SUCCEEDS  
```  
Geometries with longitude 180 or -180 now seem to work for the few I have tested.   
Going deeper into this shows that on boost version 1.82 replacing latitude 90 or -90 by 90 - 1e10 or -90+1e10 provided good results but now you need to drop further in precision to workaround this issue by replacing it with  90 - 1e4 or -90+1e4 on boost version >= 1.83.

---

## Comment 1

> Username: vissarion  
> Created at: 2024-05-13 13:12:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279#issuecomment-2107543106  

@rorviksam thanks for the report. It seems that the problem is the redundant point that represents the pole (note that both points (163 -90) and (75 -90) represent the same point i.e. the south Pole).   
  
Is it possible to create a case with a single pole point? (i.e. in your example only one of (163 -90), (75 -90) should appear in the definition of the polygon).   
  
In any case this should be fixed.

---

## Comment 2

> Username: rorviksam  
> Created at: 2024-05-15 08:04:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279#issuecomment-2111840970  

@vissarion We can't remove any of the points (163 -90), (75 -90) as it would change the shape of the polygon and will not represent the same polygon.

---

## Comment 3

> Username: vissarion  
> Created at: 2024-05-15 08:16:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279#issuecomment-2111865338  

@rorviksam But they are the same point i.e. the South pole, actually all points $(x,-90)$, where $x\in (-180,180]$ represent the same point, the South pole. In other words if the latitude is -90 the choice of the longitude does not matter.

---

## Comment 4

> Username: vissarion  
> Created at: 2024-05-15 10:17:26 UTC  
> Updated at: 2024-05-15 10:17:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279#issuecomment-2112123863  

For example the following code will return `false` for `covers`, `true` for `covers2` and `eq=1`.  
```cpp  
    namespace bg = boost::geometry;  
    namespace bgs = boost::geometry::strategy;  
    using std::string_literals::operator""s;  
    using Point = bg::model::point<double, 2, bg::cs::geographic<bg::degree>>;  
    using Polygon = bg::model::polygon<Point, true>;  
    Point pt_in_melbourne;  
    Polygon melbourne, melbourne2;  
    bg::read_wkt(  
        "POLYGON ((75 -90, 75 -6, 78 -2, 92 -2, 107 -12, 114.5 -12, 115.14222222222223 "  
        "-14.136944444444444, 126.05888888888889 -23.396944444444443, 131.84 -21.2025, "  
        "136.32888888888888 -21.499722222222225, 138.39 -26.225277777777777, 146.53333333333333 "  
        "-29, 151.970694083691 -33.4304025266695, 152.93055555555554 -35.31638888888889, "  
        "150.32055555555556 -38.18861111111111, 150 -45, 163 -45, 163 -90, 75 -90))"s, melbourne);  
    bg::read_wkt(  
        "POLYGON ((75 -90, 75 -6, 78 -2, 92 -2, 107 -12, 114.5 -12, 115.14222222222223 "  
        "-14.136944444444444, 126.05888888888889 -23.396944444444443, 131.84 -21.2025, "  
        "136.32888888888888 -21.499722222222225, 138.39 -26.225277777777777, 146.53333333333333 "  
        "-29, 151.970694083691 -33.4304025266695, 152.93055555555554 -35.31638888888889, "  
        "150.32055555555556 -38.18861111111111, 150 -45, 163 -45, 75 -90))"s, melbourne2);  
    bg::read_wkt("POINT(119 -46.0250000000001)"s, pt_in_melbourne);  
    bool const covers = bg::covered_by(pt_in_melbourne, melbourne,  
                                       bgs::within::geographic_winding<bgs::vincenty>());  
    bool const covers2 = bg::covered_by(pt_in_melbourne, melbourne2,  
                                       bgs::within::geographic_winding<bgs::vincenty>());  
    std::cout << "eq=" << bg::equals(melbourne2, melbourne) << std::endl;  
```

---

## Comment 5

> Username: rorviksam  
> Created at: 2024-05-16 09:51:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279#issuecomment-2114736711  

@vissarion Though the point is in both polygons, they don't describe the same shape. You can easily visualize this using a tool like [dbeaver](https://dbeaver.io/) running this SQL statements.  
```sql  
select 'p1', st_geometryfromtext('POINT(119 -46.0250000000001)')   
union all  
select 'poly1', st_geometryfromtext(concat('POLYGON ((75 -90, 75 -6, 78 -2, 92 -2, 107 -12, 114.5 -12, ',  
'115.14222222222223 -14.136944444444444, 126.05888888888889 -23.396944444444443, 131.84 -21.2025, ',  
'136.32888888888888 -21.499722222222225, 138.39 -26.225277777777777, 146.53333333333333 -29, ',  
'151.970694083691 -33.4304025266695, 152.93055555555554 -35.31638888888889, ',  
'150.32055555555556 -38.18861111111111, 150 -45, 163 -45, 163 -90, 75 -90))'))  
union all  
select 'poly2', st_geometryfromtext(concat('POLYGON ((75 -90, 75 -6, 78 -2, 92 -2, 107 -12, 114.5 -12, ',  
'115.14222222222223 -14.136944444444444, 126.05888888888889 -23.396944444444443, 131.84 -21.2025, ',  
'136.32888888888888 -21.499722222222225, 138.39 -26.225277777777777, 146.53333333333333 -29, ',  
'151.970694083691 -33.4304025266695, 152.93055555555554 -35.31638888888889, 150.32055555555556 -38.18861111111111, ',  
'150 -45, 163 -45, 75 -90))'));  
```  
Take a look at the output of this query when visualized  
![Screenshot from 2024-05-16 10-50-22](https://github.com/boostorg/geometry/assets/26537091/6f1c08e3-6996-49f1-8f07-c382820feb08)  
![Screenshot from 2024-05-16 10-50-12](https://github.com/boostorg/geometry/assets/26537091/83cbe21f-880c-4fa3-b60f-2064e912eba4)  
![Screenshot from 2024-05-16 10-50-06](https://github.com/boostorg/geometry/assets/26537091/ac602b12-be4e-4431-b9ba-1e184c660ec8)  
![Screenshot from 2024-05-16 10-49-58](https://github.com/boostorg/geometry/assets/26537091/59fb2dbc-a1f5-4427-83d7-c78630fc4777)

---

## Comment 6

> Username: vissarion  
> Created at: 2024-05-16 10:52:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279#issuecomment-2114887303  

What you are visualizing is in cartesian coordinates (i.e. you are using the lon,lat geographic coordinates directly as cartesian coordinates on the Web Mercator Projection---epsg:4326, which is in general wrong because you have to project the geographic polygon to get the "projected" cartesian coordinates) that is why you are creating two different polygons.   
  
Those polygons are different indeed but they are not related to the polygons of the issue filed which use geographic coordinates (i.e. they live on a globe).   
  
The two polygons look like the following picture.  
![Screenshot from 2024-05-16 13-45-36](https://github.com/boostorg/geometry/assets/3660366/361ddace-6a40-4b30-9b08-6011ac8ae4e7)

---

## Comment 7

> Username: rorviksam  
> Created at: 2024-05-16 11:06:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/1279#issuecomment-2114932159  

@vissarion Accurate thanks for drawing my attention to this.
