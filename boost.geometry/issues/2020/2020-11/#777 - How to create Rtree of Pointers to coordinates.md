# #777 - How to create Rtree of Pointers to coordinates [Open]

> Username: mboedigh  
> Created at: 2020-11-24 20:47:33 UTC  
> Updated at: 2020-11-26 23:41:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/777  

Sorry If I missed this in the documentation. I've been searching for a while, but I don't see it.   
I have been using code similar to that shown below for a while. I can no longer compile after upgrading to boost::geometry1.73.  I believe I was using ~1.6x. My question is how can I do this in 1.73?    
Note. this seems similar to the documented example with shared_ptr, but that example compiles (even with rtree.remove added to the sample), while this one chokes on rtree.remove). I'm compiling with visual studio 16.8.2  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/register/point.hpp>  
  
namespace bg  = boost::geometry;  
namespace bgi = boost::geometry::index;  
namespace bgm = boost::geometry::model;  
namespace cs  = boost::geometry::cs;  
struct Point {  
    float x;  
    float y;  
};  
  
// this is actually a large structure. fields omitted to simplify  
struct Unit {  
    Point pos;  
};  
  
using UnitPtr = Unit const *;  
  
struct indexable {  
    using V = UnitPtr;  
  
    typedef Unit const &result_type;  
    const Unit &        operator()(V const &v) const { return *v; }  
};  
  
BOOST_GEOMETRY_REGISTER_POINT_2D(Unit, float, cs::cartesian, pos.x, pos.y)  
  
int main() {  
    using Rtree = bgi::rtree<UnitPtr, bgi::linear<64, 8> >;  
    Rtree rtree;  
  
    Unit a;  
    rtree.insert(&a);  
    rtree.remove(&a); // problem here with ambiguous template   
// could be 'boost::geometry::index::detail::equals<Geometry,boost::geometry::point_tag>'  
// or            'boost::geometry::index::detail::equals<Geometry*,Tag>'  
  
    return 0;  
}  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-11-24 23:44:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-733300650  

Thanks for reporting/asking about it. This looks like a bug or at least an inconsistency in how pointers to geometries are treated.  
  
----  
  
A workaround for this issue is to define your own `equal_to` along with your own `indexable`:  
```  
struct indexable {  
    typedef Unit const& result_type;  
    result_type operator()(UnitPtr v) const  
    {  
        return *v;  
    }  
};  
  
struct equal_to {  
    typedef bool result_type;  
    bool operator()(UnitPtr l, UnitPtr r) const {  
        return bg::equals(*l, *r);  
        // or  
        // return l == r  
    }  
};  
```  
and then pass it into the R-tree:  
```  
using Rtree = bgi::rtree<UnitPtr, bgi::linear<64, 8>, indexable, equal_to>;  
```  
  
----  
  
However I'd encourage you to try something different and store points in the R-tree directly in order to improve data locality and decrease the number of cache misses when accessing the points. This way the R-tree queries should be faster. In other words e.g. register `Point` as Boost.Geometry point type:  
```  
BOOST_GEOMETRY_REGISTER_POINT_2D(Point, float, cs::cartesian, x, y)  
```  
and then keeping the Value as small as possible store `std::pair<Point, UnitPtr>` in the R-tree. In this case the R-tree would work out of the box without custom `indexable` and `equal_to` getters. Of course you could use your own structures instead of `std::pair<>` but then you'd still need custom getters.  
  
If you decided to try it and did some benchmarking could you share the results? I'm curious if my prediction is correct.

---

## Comment 2

> Username: mboedigh  
> Created at: 2020-11-25 20:44:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-733937658  

Thank you Adam. This work around did compile.   
In ran informal benchmark with setup similar to what I encounter: 1000 units. I timed inserts, with one call to clear after 1000 inserts and I timed qbegin( nearest(...)). I complied with /O2. Tests were repeated 10,000 times. There was less than 1% difference, but it was as you predicted. I'm happy to run some additional tests if you'd like to see a different setup.

---

## Comment 3

> Username: mboedigh  
> Created at: 2020-11-25 20:45:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-733937913  

sorry, maybe I shouldn't have closed that

---

## Comment 4

> Username: awulkiew  
> Created at: 2020-11-25 21:12:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-733947830  

> I'm happy to run some additional tests if you'd like to see a different setup.  
  
No, that's fine. Thanks for sharing! I think it makes sense. In typical case internal nodes (vs leaf nodes) are accessed most frequently and they are the same no matter if value type is pointer or not. The impact of the value type should scale with the number of values (elements of leaf nodes) accessed during query. So the performance difference will probably be greater when:  
- more values are stored in nodes,  
- area of search is greater in case of spatial query like `bgi::intersects`,  
- number of required knn values is greater in case of knn query/`bgi::nearest` predicate.  
And I think typically all of them are fairly small because then the pruning strength of the R-tree is the greatest. If great numbers of values has to be accessed (caused e.g. by great overall overlap of values or big areas of searching, etc.) then at some point it makes more sense to keep values in a vector.  
  
Btw, why did you choose linear balancing algorithm and 64 max elements? Was this choice based on benchmarking of you specific use case or something else?

---

## Comment 5

> Username: mboedigh  
> Created at: 2020-11-26 18:04:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-734429011  

It appears to me that `rtree.qbegin(bgi::nearest(pos, N))` now completely sorts up to N observations when the first pointer is dereferenced? was that always the case? I was somehow under the impression that the previous behavior was to pay   
incrementally for a partial sort of one more element when the iterator was advanced.   
  
Also note, the above timings may be mistaken, because I didn't deference the result of qbegin(). It now appears that your your suggestion and the above list of considerations is supported by the timings. I'm hesitant to give any more actual results, because I'm apparently a noob and don't want to mislead anyone ;(  
  
I did good comparisons of my use case using R-tree and vectors, and I'm convinced that I better off with R-tree by many fold. I compared balancing algorithms a while ago, but I should probably repeat those to make sure I didn't make a similar mistake. Can you recommend a starting algorithm, or point me to a guide?

---

## Comment 6

> Username: awulkiew  
> Created at: 2020-11-26 19:24:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-734451453  

> It appears to me that rtree.qbegin(bgi::nearest(pos, N)) now completely sorts up to N observations when the first pointer is dereferenced?  
  
The knn query iterator should access all values that have to be accessed to make sure that a value is indeed the closest one. I.e. if the first found closest value is further than another leaf node then all of the values from this other leaf node have to be checked as well before we can tell that a value is the closest one.  
  
So the number of accessed values will depend on the tree structure. This effect will be more prominent the bigger the leaf nodes and the more overlap between nodes there is. E.g. I suggest to replace linear balancing with rstar which creates better tree structure and to decrease the number of values stored in nodes from 64 to 4. Then you should probably see that less values are accessed. If this doesn't do the trick then you could simply pass a lot greater N into `nearest()` predicate.  
  
> Also note, the above timings may be mistaken (...)  
  
Sure, no problem. It's the case with all benchmarks anyway. The details of your specific use case, your data, etc. are very important so users should do their own benchmarks anyway. I was simply curious.  
  
> I compared balancing algorithms a while ago, but I should probably repeat those to make sure I didn't make a similar mistake. Can you recommend a starting algorithm, or point me to a guide?  
  
In the docs there is a comparison of all algorithms:  
https://www.boost.org/doc/libs/1_74_0/libs/geometry/doc/html/geometry/spatial_indexes/introduction.html  
  
First of all you must think about your data. Do you store points or boxes/segments, are they relatively big and can overlap or not? You're storing points which means this is the case with the least overlap. So I'd keep the max number of values low. I'd start benchmarking from a number as low as 2.  
  
Then you have to consider how many times do you update the tree vs how many times do you preform the query. If you update the tree much then I'd start from linear balancing algorithm, if not then I'd start from rstar.  
  
Both choices above will probably interfere with each other, i.e. more complex balancing algorithm (rstar vs linear) will have the tendency to generate tree structure with lesser internal overlap.  
  
Finally, packing algorithm will generate the fastest tree. So if you can pass all of the values into the R-tree constructor and don't modify the tree afterwards you should do this. In this case you can ignore balancing algorithm.

---

## Comment 7

> Username: mboedigh  
> Created at: 2020-11-26 23:18:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-734505054  

Today is thanksgiving here, and I'm thankful for your help! I actually saw all the comparisons in the docs, but when I did testing way back when, it didn't really reflect that, and I wasn't ever sure why. That aside, I think I should use packing because I create the tree 25 times per second and each tree is constant. **I was never sure how to invoke the packing algorithm. Is it as simple as use the constructor taking an iterator?**   
In your reply you imply that using packing ignores balancing algorithm.  Now I wonder whether my old testing (years ago) had inadvertently used that. That would explain a lot.

---

## Comment 8

> Username: awulkiew  
> Created at: 2020-11-26 23:41:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/777#issuecomment-734508450  

Happy Thanksgiving!  
  
> I was never sure how to invoke the packing algorithm. Is it as simple as use the constructor taking an iterator?  
  
Yes, you can either pass a pair of iterators defining a range of values or a Boost.Range object. Since standard containers are also ranges they work out of the box, e.g.:  
```  
std::vector<Value> vec;  
rtree<Value, rstar<4>> rt1(vec.begin(), vec.end());  
rtree<Value, rstar<4>> rt2(vec);  
```  
It is also possible to create elements of this range "on the fly" by transforming some other range, like that:  
  
https://www.boost.org/doc/libs/1_74_0/libs/geometry/doc/html/geometry/spatial_indexes/rtree_examples/range_adaptors.html  
  
So there is no need to create an intermediate container. Though in reality it is created internally in the constructor anyway.  
  
> In your reply you imply that using packing ignores balancing algorithm.  
  
Yes, balancing algorithm is only used in `insert()` and `remove()`.
