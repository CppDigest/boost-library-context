# #480 - Empty intersection on polys touching the north pole [Closed]

> Username: remster  
> Created at: 2018-05-08 13:35:15 UTC  
> Updated at: 2024-10-02 12:54:36 UTC  
> Closed at: 2024-10-02 12:54:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/480  

Consider the test case:  
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
  
BOOST_AUTO_TEST_CASE(usaIntersectBigTile90) {  
  auto bigTileWKt     = "POLYGON((-182.25 -2.25,-182.25 85,-90 85,4.5 85,4.5 -2.25,-90 -2.25,-182.25 -2.25))";  
  auto bigTileWKtOn90 = "POLYGON((-182.25 -2.25,-182.25 90,-90 90,4.5 90,4.5 -2.25,-90 -2.25,-182.25 -2.25))";  
  auto usaWkt         = "POLYGON((-125.0840938165823 48.18451089135078,-123.2484442951568 48.28402780487058,-123.3222396381058 49.00207166780857,-106.2294164816153 48.99936106028161,-94.95738881549684 49.37019439405368,-91.56749988130491 48.04377777876205,-88.36986182651864 48.30606296052222,-82.51861102256366 45.33863888434129,-82.67972207213995 41.67655556472526,-74.97255548445517 44.98347221572623,-71.50101930291031 45.01344579588383,-69.22446981284281 47.4598397141629,-67.79019834014535 47.06722478688557,-67.04184250949656 44.36199518537477,-70.23005912341451 43.2124078816444,-69.75267183371977 41.13341339615248,-73.71086681700677 40.38246941502378,-75.73925419455345 36.92336928786909,-75.32570262625269 35.10289000177789,-81.00038138644489 31.36986317745486,-80.21558018606714 24.87617100882401,-82.31642199133823 24.40399007010678,-81.37277484337314 25.35110770766313,-82.98991779576328 28.87424738341057,-86.63551567900944 30.19587268651689,-88.60142307258893 30.01998991835538,-89.40197910608261 28.705405956028,-94.4542152059084 29.30489831467678,-96.8117767445914 27.80056771982362,-97.42440989188675 25.84041996728249,-99.0845302237628 26.39797732094523,-101.4011198718317 29.77120000013631,-103.2862798604784 28.97691995351909,-106.4506798904261 31.76422996252971,-111.074824919583 31.33223902311842,-114.7199604399525 32.71865525370477,-117.4636899326359 32.58936795833381,-118.1537433047147 33.49758670948682,-118.5200689113933 32.61376470726387,-119.9333407371885 33.34100477748434,-118.6596572250774 33.83057945961541,-120.7240783642898 33.9956452157932,-124.7155563780111 40.37995067155807,-125.0840938165823 48.18451089135078))";  
  
  spherical::polygon bigTile,bigTileOn90,usa;  
  boost::geometry::read_wkt(bigTileWKt, bigTile);  
  boost::geometry::read_wkt(bigTileWKtOn90, bigTileOn90);  
  boost::geometry::read_wkt(usaWkt, usa);  
  
  spherical::multipolygon out;  
  boost::geometry::intersection(bigTileOn90,usa,out);  
  std::cout << "Spherical intersection USA with big tile on 90N: " << out.size() << std::endl;  
  boost::geometry::intersection(bigTile,usa,out);  
  std::cout << "Spherical intersection USA with big tile on 85N: " << boost::geometry::wkt(out) << std::endl;  
}  
```  
with output:  
```  
Spherical intersection USA with big tile on 90N: 0  
Spherical intersection USA with big tile on 85N: MULTIPOLYGON(((-125.084 48.1845,-123.248 48.284,-123.322 49.0021,-106.229 48.9994,-94.9574 49.3702,-91.5675 48.0438,-88.3699 48.3061,-82.5186 45.3386,-82.6797 41.6766,-74.9726 44.9835,-71.501 45.0134,-69.2245 47.4598,-67.7902 47.0672,-67.0418 44.362,-70.2301 43.2124,-69.7527 41.1334,-73.7109 40.3825,-75.7393 36.9234,-75.3257 35.1029,-81.0004 31.3699,-80.2156 24.8762,-82.3164 24.404,-81.3728 25.3511,-82.9899 28.8742,-86.6355 30.1959,-88.6014 30.02,-89.402 28.7054,-94.4542 29.3049,-96.8118 27.8006,-97.4244 25.8404,-99.0845 26.398,-101.401 29.7712,-103.286 28.9769,-106.451 31.7642,-111.075 31.3322,-114.72 32.7187,-117.464 32.5894,-118.154 33.4976,-118.52 32.6138,-119.933 33.341,-118.66 33.8306,-120.724 33.9956,-124.716 40.38,-125.084 48.1845)))  
```  
This attempts to find an intersection of `usa` with a big poly *tile* stretching all the north-west wedge/coordinates of the world:  
![usa](https://user-images.githubusercontent.com/8327961/39760149-ebdb0fcc-52d4-11e8-9873-a206b7307101.png)  
  
It succeeds where the *tile* misses the north pole by 5 degrees:  
![85usa](https://user-images.githubusercontent.com/8327961/39760272-33c27f6e-52d5-11e8-94b3-8ccdc9d1895f.png)  
and stretches north only up to 85 parallel. But it fails when the *tile* stretches all the way to the pole.  
  
I can understand that the pole is a mathematical singularity for spherical coordinates, but:  
- What's boost philosophy for this obvious corner case?  
- If geometries stretching towards the pole become invalid, at what point are they actually invalid? My tiles do stretch whole arcs. I can snap them to 85 - but is this the right thing to do?

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-06-21 12:54:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/480#issuecomment-399093361  

In principle both points on pole should be treated as being equal so duplicated and one of them should be ignored AFAIR. So this tile should be treated as polygon with 3 vertices. This requires more investigation.

---

## Comment 2

> Username: CG-SD  
> Created at: 2020-10-09 15:18:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/480#issuecomment-706242657  

hi, i'm having similar issues with the intersects method with boost 1.69.  
i've looked into similar issues and it looks like the scaling method is responsible for this by expanding +/- 90° coordinates to 90.0000000001 from what i understood.  
 I tried to define BOOST_GEOMETRY_NO_ROBUSTNESS to avoid rescaling without success.  
Is there any workaround other than replacing every 90 in latitude by 89.999999999999999 in my geometries?  
How to follow progress on that matter?  
thanks,

---

## Comment 3

> Username: vissarion  
> Created at: 2023-07-28 10:12:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/480#issuecomment-1655435480  

Please let me know if this is fixed after merging PR  https://github.com/boostorg/geometry/pull/1177

---

## Comment 4

> Username: vissarion  
> Created at: 2024-10-02 12:54:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/480#issuecomment-2388576452  

It seems that indeed this is fixed. A test case is added by PR https://github.com/boostorg/geometry/pull/1320
