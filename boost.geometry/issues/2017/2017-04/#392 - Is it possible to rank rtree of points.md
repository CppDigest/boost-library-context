# #392 - Is it possible to rank rtree of points? [Closed]

> Username: agauniyal  
> Created at: 2017-04-09 15:09:38 UTC  
> Updated at: 2017-04-12 16:00:00 UTC  
> Closed at: 2017-04-10 10:49:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/392  

I've a Ranked point structure which looks like this -  
  
```  
struct Point {  
	Float x;  
	Float y;  
	Point(Float _x, Float _y) : x(_x), y(_y) {}  
};  
  
struct RankedPoint {  
	Point point;  
	int rank;  
	RankedPoint(Point _p, int _rank) : point(_p), rank(_rank) {}  
	RankedPoint(Float _x, Float _y, int _rank) : point(_x, _y), rank(_rank) {}  
};  
```  
  
And I'm using `boost.geometry`'s registration Macros to register these eg  
`BOOST_GEOMETRY_REGISTER_POINT_2D(RankedPoint, float, cs::cartesian, point.x,  
                                 point.y)`  
  
I also have a Rectangle structure which corresponds to `Box` concept of boost.geometry eg  
```  
struct Rectangle {  
	Point min_corner;  
	Point max_corner;  
	Rectangle(Point _min, Point _max) : min_corner(_min), max_corner(_max) {}  
	Rectangle(Float _x1, Float _y1, Float _x2, Float _y2)  
	    : min_corner(_x1, _y1), max_corner(_x2, _y2)  
	{  
	}  
};  
  
BOOST_GEOMETRY_REGISTER_POINT_2D(Point, float, cs::cartesian, x, y)  
BOOST_GEOMETRY_REGISTER_BOX(Rectangle, Point, min_corner, max_corner)  
```  
  
Now the task I'm trying to do deals with getting only 100 points ranked by rank(lowest is better) inside a box. So first I create a rtree of points - `bgi::rtree<RankedPoint, bgi::dynamic_rstar> tempRT(points, bgi::dynamic_rstar(points.size()));` and then create a `Rectangle` and use rtree's query method eg -  
  
```  
struct Extract_Ranks {  
	void operator()(const RankedPoint &p) { Top100Result.push_back(p); }  
} callback;  
  
rT.query(bgi::covered_by(rectangle1), boost::make_function_output_iterator(callback));  
```  
  
The problem with this approach is that I've to extract all the points in that rectangle and then sort them by rank and then use starting 100 and delete others. So I was thinking, isn't there a way to either store the points inside rtree sorted by rank or somehow retrieve them sorted by rank so that I don't have to pull millions of points just for top 100 ones.

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-04-09 19:11:00 UTC  
> Updated at: 2017-04-09 20:34:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-292805940  

There is no such mechanism inside the rtree but you can easily add it into `Extract_Ranks` callback. Just use heap instead of a plain vector. For number of found elements lesser than N just add points into the vector. For number of elements found equal to N make heap on vector. For the number of elements greater than N add newly found element into the heap if its rank is lesser than currently the greatest rank.  
  
It's similar to kNN query but instead of N smallest distances you're interested in N lowest ranks. See how kNN query is implemented, this is the storing part:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/visitors/distance_query.hpp#L32  
  
It would probably be possible to add to the library some kind of user-defined kNN query predicate allowing the user to define how distances are calculated etc. but currently this is not possible.

---

## Comment 2

> Username: agauniyal  
> Created at: 2017-04-10 10:49:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-292915193  

Hmm but that heap method will still extract all millions of points but hide it away under an abstraction, right? Not sure why, but my binary search method on sorted array of points is working much much faster than rtree.

---

## Comment 3

> Username: awulkiew  
> Created at: 2017-04-10 15:10:02 UTC  
> Updated at: 2017-04-10 15:10:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-292979350  

The heap method will extract all points `covered_by` a rectangle, how many it depends on the actual data and `rectangle1`.  
  
How big is your rectangle WRT the whole R-tree? Are you really interested in spatial querying or is this `rectangle1` passed only to trigger the query somehow and you only want to get the Points with lowest rank from the whole R-tree? I have an impression that there is some detail missing.  
  
> Not sure why, but my binary search method on sorted array of points is working much much faster than rtree.  
  
It's doable in some cases to implement spatial indexing like that but extracting points covered by a rectangle would require more work than simple binary search. E.g. you'd have to represent coordinates with a position on a space filling curve. So my guess is that you're doing something else than spatial indexing. And if that's the case then indeed using R-tree won't be good for that. If your goal is to get N Points with smallest rank and `rectangle1` covers the whole R-tree then all Points stored in the R-tree are traversed so the indexing is not used at all. How are you sorting Points in your array?

---

## Comment 4

> Username: agauniyal  
> Created at: 2017-04-11 00:52:21 UTC  
> Updated at: 2017-04-11 01:15:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-293118876  

@awulkiew I have a list of coordinates in `x y rank` format ranging from 10^6 - 10^8 easily. There are 100-1000s of rectangles provided on which I've to iterate and extract some low(~100) amount of ranked points for each of them. So what I'm doing is pretty simple here ie  
  
```cpp  
// build a rtree with points vector  
bgi::rtree<RankedPoint, bgi::dynamic_rstar> rT(points, bgi::dynamic_rstar(points.size()));  
  
// iterate over all rectangles and query for points  
for (const auto &r : rectangles) {  
	ranks.clear();  
	rT.query(bgi::covered_by(r), boost::make_function_output_iterator(callback));  
	ranksVec.emplace_back(ranks);  
}  
```  
  
where callback is simply defined as -  
```cpp  
struct Extract_Ranks {  
	void operator()(const RankedPoint &p) { ranks.push_back(p.rank); }  
} callback;  
```  
  
and I'm sorting the ranks and picking top 100 ranks somewhere else but that doesn't matter since above code is the one taking most of the time.  
  
There might be a rectangle which could cover all points but it is very unlikely and there will be always rectangles that only happen to have few thousand points inside them out of 10^6/7 etc.  
  
Also I'm sorting points both by x and by y coordinates in two different arrays, and choose the one with respect to the shape of rectangle eg is it lengthwise longer or heightwise. Then I perform a binary search on the array to limit to rectangle's `min_x` and `max_x` and then search linearly for `y` points that fall into rectangle.

---

## Comment 5

> Username: agauniyal  
> Created at: 2017-04-11 01:08:33 UTC  
> Updated at: 2017-04-11 01:09:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-293121001  

Here is a sample run on 10^7 points and only 10 rectangles -  
  
```  
45117μs  
39465μs  
22250μs  
47050μs  
21848μs  
35770μs  
33947μs  
27171μs  
20953μs  
14307μs  
Total - 308ms  
13.69user 0.11system 0:13.81elapsed 99%CPU (0avgtext+0avgdata 394868maxresident)k  
0inputs+0outputs (0major+9287minor)pagefaults 0swaps  
```  
  
Here I'm using exactly same dataset but instead of rtree I use arrays and binary search on them -  
  
```  
Searching in X: 681points- 33μs  
Searching in X: 35275points- 335μs  
Searching in X: 49205points- 164μs  
Searching in X: 87927points- 375μs  
Searching in X: 84274points- 263μs  
Searching in X: 941274points- 3374μs  
Searching in X: 1370525points- 10000μs  
Searching in X: 1888963points- 11569μs  
Searching in X: 5856456points- 50764μs  
Searching in Y: 2615917points- 17884μs  
Total - 94ms  
13.27user 0.11system 0:13.40elapsed 99%CPU (0avgtext+0avgdata 277900maxresident)k  
0inputs+0outputs (0major+8148minor)pagefaults 0swaps  
```  
  
*timings in microsecs

---

## Comment 6

> Username: awulkiew  
> Created at: 2017-04-11 09:42:10 UTC  
> Updated at: 2017-04-11 09:55:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-293206276  

Right, thanks for the explanation, So AFAIU you're sorting in one axis and then finding lower and upper bound of a rectangle. This might be faster for some specific dataset but let's leave that for a moment because I noticed something in the code that is probably the reason of poor performance.  
  
In the code the number of all points `points.size()` is passed into `bgi::dynamic_rstar` which defines the **number of elements in a node** of the R-tree. This means that in the R-tree there is only one huge node containing all of the points. So your R-tree **works like a vector** of points iterating through all of them while performing the query.  
  
Some smaller number should be passed, i.e. from 2 to 32 depending on your dataset and how are you using the R-tree. Consider other typical datastructures for spatial indexing, kd-tree node has 2 children nodes, quadtree node has 4, octree has 8. Moreover there is no need to use `dynamic_*`  predicate in your case. Plus you're creating the R-tree passing the points into the constructor which triggers packing algorithm, so no balancing algorithm used. And taking into account that you're storing Points, there is no overlap of objects.  
  
So I'd use some small number for max number of elements in a node, i.e. 2 or 4 (just test which one is better for you) and use static parameters, e.g.:  
  
    bgi::rtree<RankedPoint, bgi::rstar<4>> rT(points);

---

## Comment 7

> Username: agauniyal  
> Created at: 2017-04-12 15:21:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-293612532  

@awulkiew thank you very much, that was the stupidiest thing I've ever done 😆 . I assumed the parameter meant the total number of points to be inserted into tree. Now it's as fast as the array version and sometimes ~2-3ms more faster, but the code has dropped to 10x less!

---

## Comment 8

> Username: awulkiew  
> Created at: 2017-04-12 16:00:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/392#issuecomment-293625410  

I'm glad that it works for you. Cheers!
