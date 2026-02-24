# #1050 - rtree with qbegin doesn't sort the elements by distance [Open]

> Username: ghost  
> Created at: 2022-08-06 07:03:26 UTC  
> Updated at: 2024-10-11 21:31:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1050  

Hi,  
I just ran into an issue with the order of the returned values with an rtree using segments.  
  
```  
using Vector2d = boost::geometry::model::d2::point_xy <double, boost::geometry::cs::cartesian>;  
using seg2d = boost::geometry::model::segment <Vector2d>;  
using rtree_seg = pair <seg2d, int>;  
boost::geometry::index::rtree <rtree_seg, boost::geometry::index::rstar <16> > global_rtree_segs;  
```  
I had an issue with the result, so I decided to use an iterative query because it should have guaranteed sorted results from closest for farthest:  
  
```  
vector <pair <seg2d, int> > results;  
for ( auto it = global_rtree_segs.qbegin( boost::geometry::index::intersects( seg ) && boost::geometry::index::nearest( seg, 100 ) ); it != global_rtree_segs.qend(); ++it ){  
  
                        results.push_back( *it );  
}  
```  
  
and the distance check:  
```  
for ( u32 idx = -1; auto &e: r ){  
        idx += 1;  
        double d = boost::geometry::distance( seg, e.first );  
        std::cout << idx << " " << boost::geometry::wkt( e.first ) << ", distance= " << d << "\n";  
  
}  
```  
And I saw that the distance are in random order.  
But the Note at [https://www.boost.org/doc/libs/1_79_0/libs/geometry/doc/html/geometry/spatial_indexes/queries.html](url) says  
  
> It's different in case of k-NN queries performed with query iterator returned by qbegin() function which guarantees the iteration over the closest Values first  
  
  
So, right now, I want all the segments intersecting with a given segment, from closest to farthest, I need to implement a sort afterwards.  
  
If I didn't get something, please tell me, otherwise, I think people should not rely on the note in the doc.

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-09-30 20:00:04 UTC  
> Updated at: 2022-09-30 20:00:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1050#issuecomment-1263968566  

Yes, the elements returned by iterative query should be sorted by (comparable) distance.  
Could you prepare a minimal example with data allowing me to reproduce the issue?

---

## Comment 2

> Username: sasha2048  
> Created at: 2024-08-25 21:41:39 UTC  
> Updated at: 2024-08-25 21:44:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1050#issuecomment-2309004840  

I confirm that it happens in Boost 1.84 when searching for points (in my case: double, 2D, cartesian) that are nearest to a segment.  
  
It affects all kind of queries, not only iterative queries: in iterative queries, the points aren't always returned in the correct order while, **in non-iterative queries, the returned points aren't always the nearest ones**.  
  
(I've compared the distances in two ways: manually and via `boost::geometry::comparable_distance` – both ways agree that it's broken.)  
  
It happens sporadically. Often just clearing the tree and reinserting the points _exactly in the same order they were inserted_ originally "fixes" the problem. However, sometimes the points needs to be shuffled before reinserted otherwise nothing changes, i.e. reinserting points in the same order changes nothing (that's kinda good, because it's more deterministic, I'll try to dump later an example of such a reinsertion-agnostic dataset, particularly the array of points and the segment).  
  
(I've tried `boost::geometry::index::linear<…>`, `boost::geometry::index::quadratic<…>` and `boost::geometry::index::rstar<*>` with various `MaxElements` – it happens for all of them, though the frequency may differ.)

---

## Comment 3

> Username: awulkiew  
> Created at: 2024-10-11 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1050#issuecomment-2408145681  

@sasha2048 thanks for the info. Do you have an example allowing to reproduce the issue?
