# #475 - Wrong union result in spherical coordinate system [Open]

> Username: remster  
> Created at: 2018-04-17 17:03:43 UTC  
> Updated at: 2018-07-30 19:53:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/475  

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
  
BOOST_AUTO_TEST_CASE(testUnionBug) {  
  auto p1wkt = "POLYGON((-78.4072265625001 43.06652924482626,-78.4072265625 43.06740063068311,-78.4063141178686 43.06653210403569,-78.4072265625001 43.06652924482626))";  
  auto p2wkt = "POLYGON((-78.55968743491499 43.06594969590624,-78.55036227331367 43.07380195109801,-78.53503704605811 43.08248347074284,-78.51769210872999 43.08880392487917,-78.49899564953199 43.09251971058174,-78.47966844278045 43.09348761253013,-78.46045580120891 43.09167037120638,-78.44209853911326 43.08713812460473,-78.42530412309867 43.08006566649393,-78.41071917768537 43.07072563376782,-78.40631359930124 43.06653210565861,-78.55968743491499 43.06594969590624))";  
  
  {  
    spherical::polygon p1,p2;  
    boost::geometry::read_wkt(p1wkt, p1);  
    boost::geometry::read_wkt(p2wkt, p2);  
  
    spherical::multipolygon out;  
    boost::geometry::union_(  
          p1,  
          p2,  
          out);  
    std::cout << "Spherical union: " << boost::geometry::wkt(out) << std::endl;  
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
    std::cout << "Cartesian union: " << boost::geometry::wkt(out) << std::endl;  
  }  
}  
```  
when executed against [Boost 1.66](https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.tar.gz,) the output is:  
```  
Spherical union: MULTIPOLYGON()  
Cartesian union: MULTIPOLYGON(((-78.5597 43.0659,-78.5504 43.0738,-78.535 43.0825,-78.5177 43.0888,-78.499 43.0925,-78.4797 43.0935,-78.4605 43.0917,-78.4421 43.0871,-78.4253 43.0801,-78.4107 43.0707,-78.4063 43.0665,-78.5597 43.0659)))  
```  
Now I assume that union can only be empty if both constituting geometries are empty and under this assumption i take it this a bug. I will continue trying to diagnose and put more info, but as diagnostics are time consuming, I am submitting the issue as i see it now.  
  
This the same general problem domain myself and @vosst have been working on (and earlier raised https://github.com/boostorg/geometry/issues/470).

---

## Comment 1

> Username: remster  
> Created at: 2018-04-18 09:21:52 UTC  
> Updated at: 2018-04-18 09:22:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-382322829  

I will just keep filling this comment up with my findings as i diagnose this.   
  
I see that cartesian and spherical scenarios start to differ [here](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/overlay.hpp#L306) where the cartesian finds no turns, whilst spherical does. Moreover `turn_info_per_ring` in the spherical case has, for both turns, `has_blocked_turn` set, which seems to influence ring selection [here](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/select_rings.hpp#L270)

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-04-20 16:38:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-383153251  

Thanks for the info. I'll look into it next week.

---

## Comment 3

> Username: remster  
> Created at: 2018-05-02 08:36:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-385904710  

@awulkiew are there any news on this? I could have another go at diagnosing this, but the code is quite cryptic. Is it described somewhere? Does it implement a specific, well known algorithm? - so i can build a mental model of what it's trying to do.

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-05-02 21:28:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386127219  

@remster AFAIK the algorithm is Boost.Geometry-specific algorithm based on graph traversal. @barendgehrels was it taken from some paper or designed from scratch?  
  
I can reproduce the bug and currently am working on the fix. I have a few possible fixes in mind (in spherical intersection strategy) but I want to debug more before I choose the best one because it's possible that the bug is located elsewhere (in overlay: handle_colocations, enrich_turns, traverse, etc).  
Is this the only case where you get wrong result in spherical CS?  
Is this something critical for you? How fast do you need the fix?  
  
FYI, replacing the long segment in the second geometry with shorter ones seems to help, so e.g.:  
  
    boost::geometry::densify(p2, p3, 0.001); // for unit sphere  
    boost::geometry::union_(p1, p3, out); // instead of p2

---

## Comment 5

> Username: remster  
> Created at: 2018-05-03 09:31:37 UTC  
> Updated at: 2018-05-03 09:55:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386239598  

Thanks for the reply @awulkiew and for diving into this.  
> Is this the only case where you get wrong result in spherical CS?   
  
Nope. Attaching more samples. I can't tell if they're down to the same/single defect or expose more. Note that we can only detect them in bulk by watching out for an empty union (which I assume - and given well formed inputs, is always an illegal output). We won't be able to do this with intersections.  
[out.txt](https://github.com/boostorg/geometry/files/1970355/out.txt)  
  
  
> Is this something critical for you? How fast do you need the fix?  
  
We're migrating our solution from gdal/geos to boost and we're doing this for two reasons:  
1. header only vs a bulky library  
2. one of our deliverables are mapbox tiles and we find it easier to generate them of off boost geometries - they're more transparent and flexible than gdal/geos. Doing so also looks about 2.2x quicker, which, to say the least, is important.  
  
We've reached the functional parity and are discovering those bugs as we harden the solution. We obviously cannot complete the migration with these defects in - so we're having a stale branch that's getting gradually harder to keep in sync. If i were convinced that this is the only bug remaining, i could wait a month for a fix, but I am not convinced.  
  
I would be happy to help diagnosing/fixing this - but I'd need a hand from you - to get me up to speed a bit.

---

## Comment 6

> Username: remster  
> Created at: 2018-05-03 15:06:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386328026  

Found another possible problem.   
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
  
BOOST_AUTO_TEST_CASE(usaIntersectionTestCase) {  
  auto p1wkt = "POLYGON((-180 -2.25,-180 80,4.5 80,4.5 -2.25,-180 -2.25))";  
  auto p2wkt = "POLYGON((-125.0840938165823 48.18451089135078,-123.2484442951568 48.28402780487058,-123.3222396381058 49.00207166780857,-106.2294164816153 48.99936106028161,-94.95738881549684 49.37019439405368,-91.56749988130491 48.04377777876205,-88.36986182651864 48.30606296052222,-82.51861102256366 45.33863888434129,-82.67972207213995 41.67655556472526,-74.97255548445517 44.98347221572623,-71.50101930291031 45.01344579588383,-69.22446981284281 47.4598397141629,-67.79019834014535 47.06722478688557,-67.04184250949656 44.36199518537477,-70.23005912341451 43.2124078816444,-69.75267183371977 41.13341339615248,-73.71086681700677 40.38246941502378,-75.73925419455345 36.92336928786909,-75.32570262625269 35.10289000177789,-81.00038138644489 31.36986317745486,-80.21558018606714 24.87617100882401,-82.31642199133823 24.40399007010678,-81.37277484337314 25.35110770766313,-82.98991779576328 28.87424738341057,-86.63551567900944 30.19587268651689,-88.60142307258893 30.01998991835538,-89.40197910608261 28.705405956028,-94.4542152059084 29.30489831467678,-96.8117767445914 27.80056771982362,-97.42440989188675 25.84041996728249,-99.0845302237628 26.39797732094523,-101.4011198718317 29.77120000013631,-103.2862798604784 28.97691995351909,-106.4506798904261 31.76422996252971,-111.074824919583 31.33223902311842,-114.7199604399525 32.71865525370477,-117.4636899326359 32.58936795833381,-118.1537433047147 33.49758670948682,-118.5200689113933 32.61376470726387,-119.9333407371885 33.34100477748434,-118.6596572250774 33.83057945961541,-120.7240783642898 33.9956452157932,-124.7155563780111 40.37995067155807,-125.0840938165823 48.18451089135078))";  
  
  {  
    spherical::polygon p1,p2;  
    boost::geometry::read_wkt(p1wkt, p1);  
    boost::geometry::read_wkt(p2wkt, p2);  
  
    spherical::multipolygon out;  
    boost::geometry::intersection(p1,p2,out);  
    std::cout << "Spherical intersection: " << boost::geometry::wkt(out) << std::endl;  
  }  
  {  
    cartesian::polygon p1,p2;  
    boost::geometry::read_wkt(p1wkt, p1);  
    boost::geometry::read_wkt(p2wkt, p2);  
  
    cartesian::multipolygon out;  
    boost::geometry::intersection(p1,p2,out);  
    std::cout << "Cartesian intersection: " << boost::geometry::wkt(out) << std::endl;  
  }  
}  
```  
prints   
```  
Spherical intersection: MULTIPOLYGON()  
Cartesian intersection: MULTIPOLYGON(((-125.084 48.1845,-123.248 48.284,-123.322 49.0021,-106.229 48.9994,-94.9574 49.3702,-91.5675 48.0438,-88.3699 48.3061,-82.5186 45.3386,-82.6797 41.6766,-74.9726 44.9835,-71.501 45.0134,-69.2245 47.4598,-67.7902 47.0672,-67.0418 44.362,-70.2301 43.2124,-69.7527 41.1334,-73.7109 40.3825,-75.7393 36.9234,-75.3257 35.1029,-81.0004 31.3699,-80.2156 24.8762,-82.3164 24.404,-81.3728 25.3511,-82.9899 28.8742,-86.6355 30.1959,-88.6014 30.02,-89.402 28.7054,-94.4542 29.3049,-96.8118 27.8006,-97.4244 25.8404,-99.0845 26.398,-101.401 29.7712,-103.286 28.9769,-106.451 31.7642,-111.075 31.3322,-114.72 32.7187,-117.464 32.5894,-118.154 33.4976,-118.52 32.6138,-119.933 33.341,-118.66 33.8306,-120.724 33.9956,-124.716 40.38,-125.084 48.1845)))  
```  
It's this case:  
![usa_bug](https://user-images.githubusercontent.com/8327961/39585100-4809791c-4ef4-11e8-8406-b03e78ff1f24.png)

---

## Comment 7

> Username: awulkiew  
> Created at: 2018-05-03 15:39:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386339381  

Thanks. I'll look into this as well. It looks like another issue. The result should be the US.  
  
But that's interesting. Just to be sure we look at this problem the same way. In spherical this is not how the tile's Polygon (`p1`) looks like. Edges of Polygons are defined by great circles in Boost.Geometry so horizontal edges of this tile are not parallels. So e.g. the intersection point of two segments will be different in spherical and cartesian because the line interpolation is different. Moreover because the edges of Polygons are defined by great circles it is not possible to represent the intersection of this tile's shape (something which in Boost.Geometry could be considered as Box with 2 edges being parallels) and a Polygon. And before you try to represent the tile as Box in the code. This will probably compile but currently a Box is internally treated as a Polygon in all coordinate systems which is wrong in non-cartesian ones.  
  
`densify()` could be used on this tile Polygon to see what it looks like on a sphere.

---

## Comment 8

> Username: remster  
> Created at: 2018-05-03 16:27:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386354907  

Sure.. the vertical edges of this tile are of course non-parallel in geometric sense, but they run along their respective meridians so i can't imagine the picture changing post-densify and when projected on a web mercator.  
[densify](https://www.boost.org/doc/libs/master/libs/geometry/doc/html/geometry/reference/algorithms/densify/densify_3.html) appears on trunk and I am working of off 1.66 so can't reach it.  
  
I assume we agree that the intersection should not be empty in the spherical coordinate system.

---

## Comment 9

> Username: awulkiew  
> Created at: 2018-05-03 17:10:04 UTC  
> Updated at: 2018-05-03 19:52:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386367659  

I'm talking about horizontal edges. When represented in Polygon they are not going along parallels so lines of the same latitude (https://en.wikipedia.org/wiki/Parallel_(latitude)). Instead they are going along great circles (https://en.wikipedia.org/wiki/Great_circle). One exception is equator.  
  
Vertical edges are going along meridians which are equivalent to great circles.  
  
Actually I'm taking back my previous statement "The result should be the US.". The Polygon is: `POLYGON((-180 -2.25,-180 80,4.5 80,4.5 -2.25,-180 -2.25))`. Consider one of the horizontal edges: `-180 80,4.5 80`. This edge will be defined by great circle crossing both of these points. Now you have 2 possibilities going west or east. The shorter path between points is choosen by Boost.Geometry. So this segment goes from longitude `-180` to longitude `4.5` on the east hemisphere (wrt. prime meridian). So from east Asia to the west, through the whole Asia to west Europe. So this Polygon does not cover the US.  
  
And taking into account all of the above this Polygon is invalid because it's CCW, not CW (it has to be reversed). Btw, Boost.Geometry considers longitude `180` as valid, not `-180`. `-180` will work but all operations will generate `180` so if you don't want to have them mixed use `180`.  
  
This is how this Polygon looks like before (red) and after (green) densification in a cartesian-like spherical coordinate space:  
  
![obraz](https://user-images.githubusercontent.com/1226951/39599488-471f4b94-4f1c-11e8-9b38-cad62dc6c51c.png)

---

## Comment 10

> Username: remster  
> Created at: 2018-05-03 18:21:08 UTC  
> Updated at: 2018-05-03 18:27:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386390210  

Very interesting - i guess i assumed the winding order will be a hint stronger than edge lengths.  
So all i have to do (if i understand correctly) is to densify the big tile poly to have its edges sufficiently shorter (certainly less than the half distance of the great circle). I think i gonna have to start working from trunk, as, correct me if i am wrong - densify is not released.  
  
Let's put that last issue aside for now - i'll test at work tomorrow. I still hope you can help us with the empty unions.   
  
BTW, does a CCW polygon become invalid (it has no defined meaning) or is it interpreted as a hole in the sphere (i.e.: it occupies the entirety of the coordinate system minus the CCW ring)? I certainly saw evidence to suggest this but it could be a side-effect of the poly's invalidness-ness.  
  
You said denisfy goes around the original problem i reported, I will scratch my head on that, but i guess it's unrelated as these geometries are small.

---

## Comment 11

> Username: awulkiew  
> Created at: 2018-05-03 19:47:32 UTC  
> Updated at: 2018-05-03 19:53:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386414561  

> So all i have to do (if i understand correctly) is to densify the big tile poly to have its edges sufficiently shorter  
  
Note that if you want to represent this tile then you don't have to use `densify()`. You can simply generate more points on the same parallel (the same latitude) on the horizontal edges. The more points you create the more accurate the tile is represented. However it also means that more points will be in the output of set operation for this Polygon representing the tile and some other Polygon intersecting the edge of the tile. In this case one Point is needed on each horizontal edge at minimum:  
  
![obraz](https://user-images.githubusercontent.com/1226951/39599250-8743b724-4f1b-11e8-8dd9-68a39a0092c3.png)  
  
> I think i gonna have to start working from trunk, as, correct me if i am wrong - densify is not released.  
  
Yes, it is a new feature in 1.67.  
  
> I still hope you can help us with the empty unions.  
  
Yes, I'm working on it.  
  
> does a CCW polygon become invalid (it has no defined meaning) or is it interpreted as a hole in the sphere (i.e.: it occupies the entirety of the coordinate system minus the CCW ring)?  
  
It depends on algorithm. Some algorithms works with such Polygons but in general they are considered as invalid at the moment. As you mentioned they are used to represent holes. It's because the same mechanisms are used for cartesian and non-cartesian Rings when some algorithm has to calculate the order. If both orders were correct then it would be impossible to calculate one of the two possible orders while analysing only the data, so Points in a Ring. This is the case in set operations for example.  
  
> You said denisfy goes around the original problem i reported, I will scratch my head on that, but i guess it's unrelated as these geometries are small.  
  
The original problem is caused by numerical issues inside the algorithm. At some point the algorithm has to check at which place on a segment the intersection point lies. If the lengths of segments are different then calculations are performed for different numbers.

---

## Comment 12

> Username: awulkiew  
> Created at: 2018-05-03 23:47:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386469679  

I have a fix for the original issue: https://github.com/boostorg/geometry/pull/479  
  
Tomorrow I'll check other cases of union you sent in the text file. Could you also check if this fix works for you? The most important are the changes in ` include/boost/geometry/strategies/spherical/intersection.hpp`, maybe also the ones in `include/boost/geometry/policies/robustness/segment_ratio.hpp`.

---

## Comment 13

> Username: remster  
> Created at: 2018-05-04 14:23:16 UTC  
> Updated at: 2018-05-04 19:15:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-386617392  

Thanks @awulkiew !  
  
I confirm that the fix solves the original problem, but i continue seeing   
[empty unions](https://github.com/boostorg/geometry/files/1975615/out.txt). Fewer of them now (119 down to 91) as i re-run my SW USA experiment and they've got bulkier (presumably because your fix managed to stich more together). Next one down is   
[attached](https://github.com/boostorg/geometry/files/1974695/unionBug.txt). Here with my rendering:  
![unionBug](https://user-images.githubusercontent.com/8327961/39643106-ed1e2b70-4fd3-11e8-91c7-5150aac32227.png)  
  
  
As I am at it and getting back to the big tile and densify we discussed earlier. I have added extra horizontal points and that has indeed helped, you can see the code here:  
```  
BOOST_AUTO_TEST_CASE(usaIntersectionTestCase) {  
  auto bigTileWKt           = "POLYGON((-182.25 -2.25,-182.25 85,        4.5 85,4.5 -2.25,          -182.25 -2.25))";  
  auto bigTileDensedWKt     = "POLYGON((-182.25 -2.25,-182.25 85,-90 85, 4.5 85,4.5 -2.25,-90 -2.25,-182.25 -2.25))";  
  auto bigTileDensedWKtOn90 = "POLYGON((-182.25 -2.25,-182.25 90,-90 90, 4.5 90,4.5 -2.25,-90 -2.25,-182.25 -2.25))";  
  auto polyWkt = "POLYGON((-125.0840938165823 48.18451089135078,-123.2484442951568 48.28402780487058,-123.3222396381058 49.00207166780857,-106.2294164816153 48.99936106028161,-94.95738881549684 49.37019439405368,-91.56749988130491 48.04377777876205,-88.36986182651864 48.30606296052222,-82.51861102256366 45.33863888434129,-82.67972207213995 41.67655556472526,-74.97255548445517 44.98347221572623,-71.50101930291031 45.01344579588383,-69.22446981284281 47.4598397141629,-67.79019834014535 47.06722478688557,-67.04184250949656 44.36199518537477,-70.23005912341451 43.2124078816444,-69.75267183371977 41.13341339615248,-73.71086681700677 40.38246941502378,-75.73925419455345 36.92336928786909,-75.32570262625269 35.10289000177789,-81.00038138644489 31.36986317745486,-80.21558018606714 24.87617100882401,-82.31642199133823 24.40399007010678,-81.37277484337314 25.35110770766313,-82.98991779576328 28.87424738341057,-86.63551567900944 30.19587268651689,-88.60142307258893 30.01998991835538,-89.40197910608261 28.705405956028,-94.4542152059084 29.30489831467678,-96.8117767445914 27.80056771982362,-97.42440989188675 25.84041996728249,-99.0845302237628 26.39797732094523,-101.4011198718317 29.77120000013631,-103.2862798604784 28.97691995351909,-106.4506798904261 31.76422996252971,-111.074824919583 31.33223902311842,-114.7199604399525 32.71865525370477,-117.4636899326359 32.58936795833381,-118.1537433047147 33.49758670948682,-118.5200689113933 32.61376470726387,-119.9333407371885 33.34100477748434,-118.6596572250774 33.83057945961541,-120.7240783642898 33.9956452157932,-124.7155563780111 40.37995067155807,-125.0840938165823 48.18451089135078))";  
  
  spherical::polygon bigTile,bigTileDensed,bigTileDensedOn90,poly;  
  boost::geometry::read_wkt(bigTileWKt, bigTile);  
  boost::geometry::read_wkt(bigTileDensedWKt, bigTileDensed);  
  boost::geometry::read_wkt(bigTileDensedWKtOn90, bigTileDensedOn90);  
  boost::geometry::read_wkt(polyWkt, poly);  
  
  spherical::multipolygon out;  
  boost::geometry::intersection(bigTile,poly,out);  
  std::cout << "Spherical intersection with big tile: " << out.size() << std::endl;  
  boost::geometry::intersection(bigTileDensedOn90,poly,out);  
  std::cout << "Spherical intersection with big densed tile on 90: " << out.size() << std::endl;  
  boost::geometry::intersection(bigTileDensed,poly,out);  
  std::cout << "Spherical intersection with big densed tile: " << boost::geometry::wkt(out) << std::endl;  
}  
```  
the output is:  
```  
Spherical intersection with big tile: 0  
Spherical intersection with big densed tile on 90: 0  
Spherical intersection with big densed tile: MULTIPOLYGON(((-125.084 48.1845,-123.248 48.284,-123.322 49.0021,-106.229 48.9994,-94.9574 49.3702,-91.5675 48.0438,-88.3699 48.3061,-82.5186 45.3386,-82.6797 41.6766,-74.9726 44.9835,-71.501 45.0134,-69.2245 47.4598,-67.7902 47.0672,-67.0418 44.362,-70.2301 43.2124,-69.7527 41.1334,-73.7109 40.3825,-75.7393 36.9234,-75.3257 35.1029,-81.0004 31.3699,-80.2156 24.8762,-82.3164 24.404,-81.3728 25.3511,-82.9899 28.8742,-86.6355 30.1959,-88.6014 30.02,-89.402 28.7054,-94.4542 29.3049,-96.8118 27.8006,-97.4244 25.8404,-99.0845 26.398,-101.401 29.7712,-103.286 28.9769,-106.451 31.7642,-111.075 31.3322,-114.72 32.7187,-117.464 32.5894,-118.154 33.4976,-118.52 32.6138,-119.933 33.341,-118.66 33.8306,-120.724 33.9956,-124.716 40.38,-125.084 48.1845)))  
```  
Which demonstrate the densing worked, but note `bigTileDensedWKtOn90`, it's only difference against the working `bigTileDensedWKt` is that it stretches all the way to the north pole. That alone seems to make it not work. I can understand that the pole is a mathematical singularity for spherical coordinates, but:  
- What's boost philosophy for this obvious corner case?  
- if geometries stretching towards the pole become invalid, at what point are they actually invalid? My tiles do stretch whole arcs. I can snap them to 85 - but is this the right thing to do?

---

## Comment 14

> Username: awulkiew  
> Created at: 2018-05-07 16:58:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-387131423  

I fixed more cases. I was going through `out.txt` file you've sent before.  
  
The problem with that file and also the new one is that in come cases the lines of separate union cases overlap with lines of other case.  For the new file it's only one place, lines 29-33. My guess is that you generated this file in multiple threads. Based on coordinates I can figure out which geometries should go together in this case so no problem there. But for the future to avoid possible mistakes it'd be good to synchronize the output preventing lines overlap.  
  
Regarding the intersection with the big tile. I'll check this out after fixing union. I'd also prefer to not mix these two things in one Issue. But I'm confused I thought your tile has edges going along meridians and parallels, not great circles. Why do you want to go through pole? Anyway, please create a new Issue for this.

---

## Comment 15

> Username: remster  
> Created at: 2018-05-07 17:57:00 UTC  
> Updated at: 2018-05-08 15:17:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-387149236  

> I fixed more cases. I was going through out.txt file you've sent before.  
> The problem with that file and also the new one is that in come cases the lines of separate union cases > overlap with lines of other case. For the new file it's only one place, lines 29-33. My guess is that you   
> generated this file in multiple threads. Based on coordinates I can figure out which geometries should   
> go together in this case so no problem there. But for the future to avoid possible mistakes it'd be good   
> to synchronize the output preventing lines overlap.  
  
You have a point. [Here's](https://github.com/boostorg/geometry/files/1984545/empty_unions_spherical.txt) the fresh dump with 84 offenders - hopefully w/o any interleaving.  
  
> Regarding the intersection with the big tile. I'll check this out after fixing union. I'd also prefer to not mix   
> these two things in one Issue. But I'm confused I thought your tile has edges going along meridians   
> and parallels, not great circles. Why do you want to go through pole? Anyway, please create a new   
> Issue for this.  
  
See: https://github.com/boostorg/geometry/issues/480

---

## Comment 16

> Username: awulkiew  
> Created at: 2018-05-09 16:17:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-387793954  

Thanks. I was able to fix more cases but there are some cases which will require to rethink how things are done on a higher level of the algorithm and more severe changes to the algorithm will probably be needed.  
  
Btw, AFAIU you're migrating from GEOS. I'm wondering why have you choosen to represent geometries in spherical CS? Aren't operations implemented in GEOS cartesian only? Or is this one of the reasons?  
  
If my understanding is correct then maybe as a temporary workaround you could use cartesian points? If necessary to avoid problems with wrapping of longitudes at antimeridian before any operation you could translate both geometries to the origin/meridian, perform operation and then translate the result back.

---

## Comment 17

> Username: remster  
> Created at: 2018-05-09 16:58:08 UTC  
> Updated at: 2018-05-09 17:00:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-387805756  

> Thanks. I was able to fix more cases but there are some cases which will require to rethink how things > are done on a higher level of the algorithm and more severe changes to the algorithm will probably be > needed.  
  
Well.. I can only be glad I could offer you some hardening material.   
  
> Btw, AFAIU you're migrating from GEOS. I'm wondering why have you choosen to represent   
> geometries in spherical CS? Aren't operations implemented in GEOS cartesian only? Or is this one of > the reasons?  
  
Afaik, GEOS is used by PostGIS as _the_ underlying implementation, and since PostGIS does `geography` (spherical coordinate system ops) then I conclude GEOS does spherical coordinates. But I may be wrong and lets put that aside.   
  
We work with airspaces and try to work out, for instance, whether a submitted flight geometry enters a specific airspace. Indeed, we work with cartesian coordinates, but some of our airspaces have edges stretching thousands of kilometers - so what we do is wrong. We could *densify* these edges (along great circles) to bring them down to a desired error and continue working with cartesian coordinates, but we see this is a work-around.   
  
> If my understanding is correct then maybe as a temporary workaround you could use cartesian points? > If necessary to avoid problems with wrapping of longitudes at antimeridian before any operation you   
> could translate both geometries to the origin/meridian, perform operation and then translate the result > back.  
  
Like i said, we have been working with cartesian coordinates - no problems with anti-meridian - our tools are happy with coordinates greater than 180 /  less than -180 and they do this well. We want to migrate to boost for non-functional reasons: (1) we see 2..3 times performance improvement (2) we have difficulties working off patched GEOS (it too has bugs and it takes 2hrs to build it) (3) Boost geometries are very transparent and lightweight. We need a modern C++ geometry model in general and we've betted on Boost (I used it extensively earlier, but in non-spatial applications).  
  
Yes we could continue working on cartesian coordinates and wait for spherical to catch up with your help, regrettably cartesian on boost don't work for us either: https://github.com/boostorg/geometry/issues/476  
We are feeding it the same input as we feed to GEOS - if that's a worthy benchmark.

---

## Comment 18

> Username: remster  
> Created at: 2018-05-14 07:49:21 UTC  
> Updated at: 2018-05-14 07:51:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-388727310  

As you indicated, spherical intesections need deeper cuts in boost/geometry and as i confirmed, we could still migrate to boost and live on cartesian ops for quite a while. Do you think https://github.com/boostorg/geometry/issues/476 also exposes more fundamental problems? and if not, do you think you could prioritise https://github.com/boostorg/geometry/issues/476 so that we can switch, hopefully by the start of June?

---

## Comment 19

> Username: awulkiew  
> Created at: 2018-05-14 11:01:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-388778749  

@barendgehrels Are you tracking these issues? Since you're working on rescaling. Could your changes fix https://github.com/boostorg/geometry/issues/476?

---

## Comment 20

> Username: remster  
> Created at: 2018-05-14 14:21:17 UTC  
> Updated at: 2018-05-14 14:29:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-388834444  

BTW, I've re-applied your [fix to date](https://github.com/boostorg/geometry/pull/479/commits/7836c7bf72d952b4d41017adc279ed803b7c92f1) and re-run the scan. I see:  
```  
main: /home/remek/go/src/github.com/airmap/platform/airmap/gis/geo/boostgeo/geometry/_build/boost_1_66_0/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp:119: static void boost::geometry::detail::overlay::base_turn_handler::assign_point(TurnInfo&, boost::geometry::detail::overlay::method_type, const IntersectionInfo&, unsigned int) [with TurnInfo = boost::geometry::detail::overlay::traversal_turn_info<airmap::geometry::PointBase2D<double>, boost::geometry::segment_ratio<double> >; IntersectionInfo = boost::geometry::segment_intersection_points<airmap::geometry::PointBase2D<double>, boost::geometry::segment_ratio<double> >]: Assertion `index < info.count' failed.  
SIGABRT: abort  
```  
for spherical union [input](https://github.com/boostorg/geometry/files/2001145/out.txt)  
rendered:   
![colorado_bug](https://user-images.githubusercontent.com/8327961/40001677-0bfcecf8-578f-11e8-8615-29539717b552.png)

---

## Comment 21

> Username: barendgehrels  
> Created at: 2018-05-14 17:46:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-388903572  

@awulkiew thanks for heads up - normally I keep an eye on them but at the moment they appear at a rate that I cannot follow all of it. Because these issues were targetted to spherical, I did not focus on them.  
I will have a look on Wednesday at the issue that you indicated.

---

## Comment 22

> Username: barendgehrels  
> Created at: 2018-05-16 15:37:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/475#issuecomment-389564847  

@awulkiew I shortly checked it in the rescaling branch, but that one is not yet prepared for this (does not compile yet). Should be fixed of course but cannot do that right now. To be continued.
