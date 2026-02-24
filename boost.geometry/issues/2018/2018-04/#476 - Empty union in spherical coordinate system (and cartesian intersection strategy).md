# #476 - Empty union in spherical coordinate system (and cartesian intersection strategy) [Open]

> Username: remster  
> Created at: 2018-04-20 16:08:54 UTC  
> Updated at: 2018-05-30 21:01:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/476  

Consider the test case:  
```  
namespace spherical {  
  using point = boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::degree> >;  
  using linestring = boost::geometry::model::linestring<point>;  
  using polygon = boost::geometry::model::polygon<point>;  
  using multipolygon = boost::geometry::model::multi_polygon<polygon>;  
  using mpoint = boost::geometry::model::multi_point<point>;  
  const double earthRadiusMts = 6371000;  
}  
  
BOOST_AUTO_TEST_CASE(unionTestCase) {  
  auto p1wkt = "POLYGON((-118.4333440130425 34.01738213274482,-118.3666773220425 34.01738213274482,-118.366677322 34.0166734220001,-118.366677321 34.0000067500001,-118.3666773209543 33.99962584406412,-118.370368063634 33.9889823854999,-118.373872409729 33.983340078,-118.383343992 33.983340078,-118.400010665 33.983340077,-118.400010663 33.9666734050001,-118.416677336 33.9666734040001,-118.433344009 33.9666734030001,-118.43334401 33.9833400750001,-118.416677338 33.983340076,-118.416677339 34.000006747,-118.433344012 34.000006747,-118.433344013 34.016673418,-118.4333440130425 34.01738213274482))";  
  auto p2wkt = "POLYGON((-118.4779357910156 33.89957063423745,-118.433344012 34.000006747,-118.416677339 34.000006747,-118.3656294438703 34.00264773892163,-118.3666773209543 33.99962584406414,-118.366677319 33.983340078,-118.4779357910156 33.89957063423745))";  
  spherical::polygon p1,p2;  
  boost::geometry::read_wkt(p1wkt, p1);  
  boost::geometry::read_wkt(p2wkt, p2);  
  
  spherical::multipolygon out;  
  boost::geometry::union_(p1,p2,out,boost::geometry::strategy::intersection::cartesian_segments<>());  
  boost::geometry::union_(p1,p2,out,boost::geometry::strategy::intersection::cartesian_segments<>());  
  std::cout << "Cartesian union empty: " << boost::geometry::wkt(out) << std::endl;  
}  
```  
with the output:  
```  
Cartesian union empty: MULTIPOLYGON()  
```  
Continuing our work with @vosst and as a workaround to https://github.com/boostorg/geometry/issues/475 we tried this, perhaps exotic approach, of using `boost::geometry::strategy::intersection::cartesian_segments<>()` over geometries defined in a spherical coordinate system. We calculated we can mostly live with the consequences in the interim.   
  
But then this happened.

---

## Comment 1

> Username: remster  
> Created at: 2018-05-08 12:45:06 UTC  
> Updated at: 2018-05-08 13:46:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/476#issuecomment-387389890  

A a note on this. It appears https://github.com/boostorg/geometry/pull/479 fixes the original problem reported, however, we continue observing two related, albeit slightly different classes of problems:  
  
1. geometries defined over points marked as living in the cartesian coordinate space unioned with the default (cartesian) strategy;  
  
2. geometries defined over points marked as living in the **spherical coordinate** space unioned with the **cartesian strategy**  
  
I assume, for any two input polygons, the result throwing them at (1) and (2) is expected to be the same. I.e.: for p1 and p2 defined in the spherical coordinate system, the caller of:  
```  
    boost::geometry::union_(  
          p1,  
          p2,  
          out,  
          boost::geometry::strategy::intersection::cartesian_segments<>());  
```  
expects to see the same result as if p1 and p2 were defined in the cartesian coordinate system. This assumption may be wrong.  
  
For types:  
```  
namespace cartesian {  
  using point = boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>;  
  using linestring = boost::geometry::model::linestring<point>;  
  using polygon = boost::geometry::model::polygon<point>;  
  using multipolygon = boost::geometry::model::multi_polygon<polygon>;  
  using mpoint = boost::geometry::model::multi_point<point>;  
}  
  
namespace spherical {  
  using point = boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::degree> >;  
  using linestring = boost::geometry::model::linestring<point>;  
  using polygon = boost::geometry::model::polygon<point>;  
  using multipolygon = boost::geometry::model::multi_polygon<polygon>;  
  using mpoint = boost::geometry::model::multi_point<point>;  
  const double earthRadiusMts = 6371000;  
}  
```  
--  
**Example of offending polygons case 1**  
```  
BOOST_AUTO_TEST_CASE(unionTestCase476_B) { // regression for https://github.com/boostorg/geometry/issues/476  
  auto p1wkt = "POLYGON((-106.866673337 39.616675967,-106.866673339 39.6333426400001,-106.850006667 39.6333426420001,-106.850006665 39.616675968,-106.866673337 39.616675967))";  
  auto p2wkt = "POLYGON((-106.8666733398705 39.64059714894774,-106.8500066678705 39.64059715051243,-106.850006667 39.6333426420001,-106.866673339 39.6333426400001,-106.866673337 39.616675967,-106.850006665 39.616675968,-106.8500066678705 39.64059715051243,-106.8333399928705 39.64059715151237,-106.8229126352122 39.64059715330757,-106.822913472838 39.6399087193366,-106.823381753459 39.634799470779,-106.824309700519 39.6297279105735,-106.82569269582 39.6247187323619,-106.82752390948 39.619796320812,-106.829794335008 39.6149846331484,-106.832492834848 39.6103070828582,-106.835606196143 39.60578642612979,-106.839119196408 39.6014446515688,-106.843014678777 39.597302873716,-106.847273636437 39.59338123088169,-106.851875305801 39.5896987877586,-106.856797267992 39.5862734433063,-106.862015558101 39.58312184431529,-106.867504781713 39.5802593050675,-106.873238238115 39.5776997334692,-106.8763732970132 39.57651724164473,-106.8763733033849 39.6000092954181,-106.8763733053849 39.61667596641806,-106.8763733073849 39.63334263941805,-106.8763732910156 39.6405971675511,-106.8666733398705 39.64059714894774))";  
  {  
    spherical::polygon p1,p2;  
    boost::geometry::read_wkt(p1wkt, p1);  
    boost::geometry::read_wkt(p2wkt, p2);  
  
    spherical::multipolygon out;  
    boost::geometry::union_(  
          p1,  
          p2,  
          out,  
          boost::geometry::strategy::intersection::cartesian_segments<>());  
    std::cout << "unionTestCase476_B Spherical points, cartesian union: " << boost::geometry::wkt(out) << std::endl;  
  }  
  {  
    cartesian::polygon p1,p2;  
    boost::geometry::read_wkt(p1wkt, p1);  
    boost::geometry::read_wkt(p2wkt, p2);  
  
    cartesian::multipolygon out;  
    boost::geometry::union_(  
          p1,  
          p2,  
          out);  
    std::cout << "unionTestCase476_B Cartesian points, cartesian union: " << boost::geometry::wkt(out) << std::endl;  
  }  
}  
```  
prints out:  
```  
unionTestCase476_B Spherical points, cartesian union: MULTIPOLYGON(((-106.85 39.6333,-106.85 39.6167,-106.85 39.6406,-106.833 39.6406,-106.823 39.6406,-106.823 39.6399,-106.823 39.6348,-106.824 39.6297,-106.826 39.6247,-106.828 39.6198,-106.83 39.615,-106.832 39.6103,-106.836 39.6058,-106.839 39.6014,-106.843 39.5973,-106.847 39.5934,-106.852 39.5897,-106.857 39.5863,-106.862 39.5831,-106.868 39.5803,-106.873 39.5777,-106.876 39.5765,-106.876 39.6,-106.876 39.6167,-106.876 39.6333,-106.876 39.6406,-106.867 39.6406,-106.85 39.6406,-106.85 39.6333)))  
unionTestCase476_B Cartesian points, cartesian union: MULTIPOLYGON()  
```  
31 more cases   
[cartesian_startegy_cartesian_points_empty_unions.txt](https://github.com/boostorg/geometry/files/1983857/cartesian_startegy_cartesian_points_empty_unions.txt)  
  
--  
**Example of offending polygons case 2**  
```  
BOOST_AUTO_TEST_CASE(unionTestCase476_B2) { // regression for https://github.com/boostorg/geometry/issues/476  
  auto p1wkt = "POLYGON((-113.9055633544918 40.88809046274819,-114.000833 40.916667,-113.9055633544918 40.91667033395694,-113.9055633544918 40.88809046274819))";  
  auto p2wkt = "POLYGON((-114.0425 40.98044076094674,-113.9055633544922 40.98041696693868,-113.9055633544918 40.91667033395694,-114.0004924897202 40.91666701191614,-113.9055633544918 40.88810195585447,-113.9055633544922 40.84654145875268,-113.9434344969155 40.84656423304324,-114.0827178955077 40.86170805054724,-114.0827178955077 40.93790952178269,-114.0827178955078 40.98041696693868,-114.0425 40.98044076094674))";  
  {  
    spherical::polygon p1,p2;  
    boost::geometry::read_wkt(p1wkt, p1);  
    boost::geometry::read_wkt(p2wkt, p2);  
  
    spherical::multipolygon out;  
    boost::geometry::union_(  
          p1,  
          p2,  
          out,  
          boost::geometry::strategy::intersection::cartesian_segments<>());  
    std::cout << "unionTestCase476_B2 Spherical points, cartesian union: " << boost::geometry::wkt(out) << std::endl;  
  }  
  {  
    cartesian::polygon p1,p2;  
    boost::geometry::read_wkt(p1wkt, p1);  
    boost::geometry::read_wkt(p2wkt, p2);  
  
    cartesian::multipolygon out;  
    boost::geometry::union_(  
          p1,  
          p2,  
          out);  
    std::cout << "unionTestCase476_B2 Cartesian points, cartesian union: " << boost::geometry::wkt(out) << std::endl;  
  }  
}  
```  
prints out:  
```  
unionTestCase476_B2 Spherical points, cartesian union: MULTIPOLYGON()  
unionTestCase476_B2 Cartesian points, cartesian union: MULTIPOLYGON(((-113.906 40.8465,-113.943 40.8466,-114.083 40.8617,-114.083 40.9379,-114.083 40.9804,-114.043 40.9804,-113.906 40.9804,-113.906 40.8465)))  
```  
50 more cases:  
[cartesian_strategy_spherical_points_empty_unions.txt](https://github.com/boostorg/geometry/files/1983889/cartesian_strategy_spherical_points_empty_unions.txt)

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-05-08 14:41:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/476#issuecomment-387426056  

Your assumption is correct. I.e. the strategy should define the coordinate system used by the operation. However currently some core operations like e.g. points comparison are performed using the coordinate system derived from point type instead of the strategy. This is wrong and should be fixed.

---

## Comment 3

> Username: remster  
> Created at: 2018-05-25 09:25:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/476#issuecomment-391996092  

Hi @barendgehrels .. my time to finish my migration is coming to an end, so I must ask whether there is any progress on this?

---

## Comment 4

> Username: barendgehrels  
> Created at: 2018-05-25 16:08:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/476#issuecomment-392106204  

No news yet, sorry. It is long term anyway, I've quite some good results (most testcases pass) but, as usual, the last bits take more time. This is probably to be released in Boost 1.69 or so.

---

## Comment 5

> Username: remster  
> Created at: 2018-05-25 16:28:15 UTC  
> Updated at: 2018-05-25 16:28:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/476#issuecomment-392111405  

Thanks @barendgehrels. Is there a branch i could test against?

---

## Comment 6

> Username: barendgehrels  
> Created at: 2018-05-30 21:01:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/476#issuecomment-393318180  

I'm working on feature/no_rescaling but it requires a specific configuration.  
I fixed compilation and tested against that branch. The bad news is that in the non-rescaling option the result seems worse. But that is not really surprising because it is not finished, and never yet tested against spherical polygons.  
The good news is that, with rescaling, the result seems better than in the main branch. I will try to merge the branch coming month (possible because all the non-rescaling work in progress is not yet enabled by default), but I'm less available than normally so I don't know when yet.
