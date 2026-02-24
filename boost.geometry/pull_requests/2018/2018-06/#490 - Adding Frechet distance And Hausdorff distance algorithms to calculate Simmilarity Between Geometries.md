# #490 Adding Frechet distance And Hausdorff distance algorithms to calculate Simmilarity Between Geometries [Merged]

> Username: yaghya  
> Created at: 2018-06-18 15:01:18 UTC  
> Updated at: 2020-10-22 13:11:25 UTC  
> Merged at: 2018-09-14 21:35:47 UTC  
> Closed at: 2018-09-14 21:35:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/490  

Implemented Frechet distance and Hausdorff distance algorithms for Linestrings. Checked the algorithms for different Coordinate System like Cartesian , Geographic and Spherical_Equatorial.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-06-18 18:00:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-398142385  

Thanks!  
  
Please change the file names to reflect the ones we already have in Boost.Geometry (lower-case with underscores).  
  
In the future commits also add tags at the beginning telling which part of the library you're modifying. In this case it could be `[algorithms] Adding Frechet Distance And Haudorff Distance algorithms`

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-18 18:01:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-129673867  

📁 include/boost/geometry/algorithms/FrechetDistance.hpp

```diff
  76 |+ 
  77 |+         size_type const a = boost::size(ls1);
  78 |+         size_type const b = boost::size(ls2);
```

> Username: awulkiew  
> Created_at: 2018-06-18 18:01:45 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r196171461  

`size_type` of `Linestring1` could be different than `size_type` of `Linestring2`.

> Username: awulkiew  
> Created_at: 2018-06-18 18:05:14 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r196172516  

Right but you have to use something in coup_map. You could use the greater type or leave it as it is for now. In practice this should be ok.

> Username: yaghya  
> Created_at: 2018-06-20 09:52:20 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r196714856  

I have updated the code and defined size_type1 for Linestring1 and size_type2 for Linestring2. And also used both these size_types in creating coup_mat.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-18 18:03:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-129674389  

📁 include/boost/geometry/algorithms/HausdorffDistance.hpp

```diff
  53 |+         
  54 |+         result_type dis_max=0,dis_min;
  55 |+         result_type const infinite = std::numeric_limits<double>::max();
```

> Username: awulkiew  
> Created_at: 2018-06-18 18:03:13 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r196171879  

`infinite` is not needed right? Also using `std::numeric_limits<double>::max()` is wrong since user-define coordinate type can support values greater than that.

> Username: yaghya  
> Created_at: 2018-06-20 09:53:18 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r196715184  

I forgot to remove it. I have removed it in the updated code.


---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-06-18 18:06:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-398144097  

The code looks good. Keep it up!

---

## Review 5 [Commented]

> Username: vissarion  
> Created_at: 2018-06-21 07:54:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-130702342  

📁 include/boost/geometry/algorithms/frechet_distance.hpp

```diff
 110 |+         for(size_type i = 0; i <a; i++)
 111 |+         {
 112 |+             for(size_type j = 0; j <b; j++)
```

> Username: vissarion  
> Created_at: 2018-06-21 07:54:02 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197040698  

"{" missing in for loop

> Username: yaghya  
> Created_at: 2018-06-25 19:19:13 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197912512  

Updated.


---

## Review 6 [Commented]

> Username: vissarion  
> Created_at: 2018-06-21 07:59:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-130703986  

📁 include/boost/geometry/algorithms/frechet_distance.hpp

```diff
 100 |+                 else if(i>0 && j>0)
 101 |+                     coup_matrix(i,j)=
 102 |+                     (std::max)((std::min)((std::min)(coup_matrix(i,j-1),coup_matrix(i-1,j)),coup_matrix(i-1,j-1)),geometry::distance(range::at(ls1,i),range::at(ls2,j),strategy));
```

> Username: vissarion  
> Created_at: 2018-06-21 07:59:12 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197042081  

wrap

> Username: yaghya  
> Created_at: 2018-06-25 19:19:29 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197912580  

Updated.


---

## Review 7 [Commented]

> Username: vissarion  
> Created_at: 2018-06-21 08:03:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-130705462  

📁 include/boost/geometry/algorithms/frechet_distance.hpp

```diff
   1 |+ /*
```

> Username: vissarion  
> Created_at: 2018-06-21 08:03:23 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197043272  

you transform your copyright to something similar to this https://github.com/BoostGSoC18/geometry/blob/78411f63c8d552189c34bb3c5589696c9c4f6c8a/include/boost/geometry/formulas/karney_direct.hpp#L1(only first 9 lines apply to you)

> Username: yaghya  
> Created_at: 2018-06-21 17:14:30 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197211778  

Ok, I will update the copyright.


---

## Comment 8

> Username: vissarion  
> Created_at: 2018-06-21 08:05:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-399013450  

Thanks, the code is good indeed. I added a few (stylistic) comments. You can have a look at this https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers for more.

---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-21 17:38:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-130921356  

📁 include/boost/geometry/algorithms/hausdorff_distance.hpp

```diff
 120 |+                 {
 121 |+                     result_type dis_min;
 122 |+                     bool is_dis_min_set = false;
```

> Username: awulkiew  
> Created_at: 2018-06-21 17:38:10 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197219131  

This part of the code is very similar to the algorithm for Ls-Ls. To avoid duplication of code you should make a helper function from this part and call it inside the algorithm for Ls-Ls and Ls-MLs. So a function taking two ranges of points, PtPtDistanceStrategy and the current max distance (which will be updated by the function, so both input and output variable).  
  
And later do the same with MLs-MLs because a part of algorithm there will be the same as this algorithm for Ls-MLs.  
  
You could name these helper functions `range_range()`, and `range_multirange()` because they could also be used for MultiPoint-MultiPoint so you should use some generic (not geometry specific) name.  
  
You could also have `point_range()` function (this would be the most inner loop you have in algorithms). And you could call it inside a loop in `range_range()` and directly in Point-MultiPoint algorithm.

> Username: awulkiew  
> Created_at: 2018-06-21 18:44:05 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197239584  

Or you could simply call `linestring_linestring()` here. Maybe it's even better.  
  
Note that you could use the same function for MultiPoint-MultiPoint, so still renaming it to `range_range()` makes sense.

> Username: yaghya  
> Created_at: 2018-06-22 23:00:05 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r197587601  

I have Updated hausdorff_distance, now I am calling linestring_linestring().


---

## Comment 10

> Username: yaghya  
> Created_at: 2018-06-23 18:21:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-399698706  

I have Update haudorff_distance for MultiLinestring_MultiLinestring and made the code generic by using range_range() and range_multi_range(). I will extend the code for point-multi_point and multi_point, soon.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2018-06-27 08:23:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-400585747  

I am a bit confused here about what the goal is. I have not looked at the Frechet distance, but looking at the Hausdorff distance it seems that what is computed is the Hausdorff distance between the vertices of the two geometries. Is that the goal?  
  
I am asking this since the Hausdorff distance between two linestrings is not the same as the Hausdorff distance of their vertices. In fact, if one wants to compute it correctly, the Voronoi diagram of at least one of the linestrings needs to to involved/computed. There are of course approximate iterative ways for computing an approximation.  
  
Here is an example pair of linestrings that can be used as a test case: `LINESTRING(1 0,0 0,0 100,1 100)` and `LINESTRING(2 0,3 0,3 100,2 100)`. For these two linestrings the actual Hausdorff distance is `3`. Looking at the vertices only we get a distance of `2`. There are other examples/configurations where the "discrete" Hausdorff distance computed via the vertices is arbitrarily larger than the actual Hausdorff distance.  
  
So the real question here is what does this PR intend to compute?

---

## Comment 12

> Username: vissarion  
> Created_at: 2018-06-27 08:51:20 UTC  
> Updated_at: 2018-06-27 08:52:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-400594057  

@mkaravel indeed, this PR implements discrete versions of both Hausdorff and Frechet distances (i.e. considering only vertices of geometries, in Frechet case their sequence too). I think it would be clearer to add the word discrete in the title of the PR as well as in the names for the algorithms in the code. This will avoid future confusions.   
  
On the other hand, implementing (a both efficient and more accurate approximation of) the continuous versions sounds interesting as a future work but also much more involved.   
  
If anyone is aware of any well known open-source code for the continuous versions of those algorithms it would be useful to post it here. It seems that PostGIS (that uses GEOS) also implements the discrete versions. See: [1](https://postgis.net/docs/ST_HausdorffDistance.html) [2](http://geos.refractions.net/ro/doxygen_docs/html/classgeos_1_1algorithm_1_1distance_1_1DiscreteHausdorffDistance.html)

---

## Comment 13

> Username: awulkiew  
> Created_at: 2018-06-27 13:45:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-400676943  

Most importantly it's a GSOC project so should be considered as work in progress. PR's title is derived from the GSOC's project title.  
  
One of the things we should figure out is how the interface should look like, how these algorithms should be semantically represented, at which level of abstraction. So function names are also temporary I think.

---

## Comment 14

> Username: yaghya  
> Created_at: 2018-07-06 06:49:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-402943210  

I have updated the test files according to the points mentioned in the mail by Adam and all tests are executed using the b2 command without any error.  
Now, should I focus on optimizing the algorithm or you would like me to work on something else?

---

## Comment 15

> Username: awulkiew  
> Created_at: 2018-07-06 13:22:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403032703  

Thanks!  
  
Please add tests for edge cases, i.e. linear rings (closed curves), triangles (minimum-length closed curves) and segments (minimum number of points in linestring) e.g.:  
  
`LINESTRING(0 0, 1 0, 1 1, 0 1, 0 0)` and `LINESTRING(0 0, 1 0, 1 1, 0 1, 0 0)`  
`LINESTRING(0 0, 1 0, 1 1, 0 1, 0 0)` and `LINESTRING(1 1, 0 1, 0 0, 1 0, 1 1)`  
`LINESTRING(0 0, 1 0, 1 1, 0 0)` and `LINESTRING(0 0, 1 0, 1 1, 0 0)`  
`LINESTRING(0 0, 1 0, 1 1, 0 0)` and `LINESTRING(1 1, 0 0, 1 0, 1 1)`  
`LINESTRING(0 0, 1 0)` and `LINESTRING(0 0, 1 0)`  
  
In all of these cases the distance should be 0 right?  
Then add test cases with the first linestring the same and the other one containing points in reversed order. For both Hausdorff and Frechet.  
  
Yes, you could experiment with improving discrete Hausdorff performance. The most obvious thing is to try to use spatial indexing, e.g. R-tree. I briefly tried to find paper describing an algorithm for discrete Hausdorff distance with complexity lower than O(NM) but I was unable to find any. On the other hand the use of R-tree seems to be obvious since the "brute-force" algorithm simply calculates distances between all points (not counting break conditions). @vissarion @mkaravel do you know if such algorithm exists?  
  
One remark regarding the R-tree. Currently it's not possible to pass strategy into query (only default strategies can be used internally) so this would require an upgrade of the R-tree but this is something that I'd do since it's out of scope of this project.  
  
You could also implement:  
  
- contineous hausdorff distance for Linestrings. This should be trivial right @vissarion @mkaravel? Instead of traversing geometries point by point do this segment by segment and pick the distance. Or am I missing something?  
  
- contineous frechet distance for Linestrings.  
  
- frechet distance for Rings (possibly concave polygons without holes) and Polygons (polygons with holes) but this AFAIU would require to implement polygon decomposition into convex parts and maybe triangulation algorithms so I'm not sure if we have enough time for that.

---

## Comment 16

> Username: yaghya  
> Created_at: 2018-07-07 14:49:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403220837  

I have added the test for edge cases. And yes all these examples ( mentioned in the above comment ) and also the reverse linestring case will give 0 as Discrete Hausdorff  Distance. But in the case of Discrete Frechet Distance   
LINESTRING(0 0, 1 0, 1 1, 0 1, 0 0) and LINESTRING(0 0, 1 0, 1 1, 0 1, 0 0)  
LINESTRING(0 0, 1 0, 1 1, 0 0) and LINESTRING(0 0, 1 0, 1 1, 0 0)  
and  
LINESTRING(0 0, 1 0) and LINESTRING(0 0, 1 0)  
will give 0 as outcome but   
  
LINESTRING(0 0, 1 0, 1 1, 0 1, 0 0) and LINESTRING(1 1, 0 1, 0 0, 1 0, 1 1) // for Cartesian CS sqrt(2.0) will be the answer  
LINESTRING(0 0, 1 0) and LINESTRING(0 0, 1 0)  // for Cartesian CS sqrt(2.0) will be the answer  
and  the reverse linestring case will not give 0 as the fechet Distance.  
  
  
I was looking into different examples using  R-tree, but I was not able to find how to insert a linestring or a range in R-tree and was only able to insert individual point separately in a R-tree. Please help me with this.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2018-07-07 20:42:33 UTC  
> Updated_at: 2018-07-07 20:58:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403241746  

In Hausdorff you have to find closest point in the most inner loop so you have to store points in the R-tree. So the type is:  
  
    typedef bgi::rtree<point_type, bgi::linear<4> > rtree_type;  
  
The R-tree will be the fastest if you create it with packing algorithm. To do this you have to pass a range info the constructor:  
  
    rtree_type rtree(boost::begin(ls), boost::end(ls));  
  
Then you can find the closest point like that (replacing inner loop):  
  
    point_type result;  
    // pass pointer only if 1 point is expected!  
    size_t found = rtree.query(bgi::nearest(pt, 1), &result);

---

## Comment 18

> Username: yaghya  
> Created_at: 2018-07-08 13:18:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403287311  

In hausdorff_distance.hpp in range_range() function I have updated the inner loop where it calls point_range() function using R-tree. I have not changed the point_range() function as when it iterates over  the range and find the minimum distance it takes O(N) whereas using R-tree will make it O(N+logN)   
O(N) for creating R-tree  
```  
rtree_type rtree(boost::begin(ls), boost::end(ls));  
```  
and O(logN) for searching in the R-tree.

---

## Comment 19

> Username: awulkiew  
> Created_at: 2018-07-08 17:44:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403303854  

Correct, using the R-tree only has sense in Range v.s. Range case.  
  
But you should have implement this version of the algorithm aside of the basic implementation. Otherwise you won't be able to compare the performance of both solutions. My guess is that the R-tree version will be faster in some cases but slower in other cases. So before we decide which version of the algorithm should be used by the library we have to write performance tests and check both of them.

---

## Comment 20

> Username: yaghya  
> Created_at: 2018-07-08 18:55:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403308589  

So should I make 2 version hausdorff_distance.hpp one with R-tree and one without, with rest of the code same.

---

## Comment 21

> Username: awulkiew  
> Created_at: 2018-07-08 19:54:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403312433  

To be precise, 2 versions of `hausdorff_distance::range_range`. You could still have one function `bg::hausdorff_distance()` function and the version of `range_range` could be picked based on preprocessor define, e.g. something like  
  
    #ifdef BOOST_GEOMETRY_ENABLE_SIMILARITY_RTREE  
    // range_range version using rtree  
    #else  
    // range_range version using loop  
    #endif  
  
Then you could write some performance tests and compile two programs, one with and one without preprocessor macro defined (`-DBOOST_GEOMETRY_ENABLE_SIMILARITY_RTREE`), to compare the performance.

---

## Comment 22

> Username: yaghya  
> Created_at: 2018-07-09 07:01:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403379618  

For Comparing the performance the of these 2 versions (without rtree and with rtree) of hausdorff_distance, I was thinking of generating different test cases and comparing the accuracies of each version, comparing the memory space required for each version and comparing the time taken by each version for these test cases. I was going through this repository  of yours https://github.com/awulkiew/benchmark-geometry do I have to do something  similar.

---

## Comment 23

> Username: awulkiew  
> Created_at: 2018-07-09 12:04:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-403456087  

Yes, you may use the code from there, include `benchmark.hpp` and then run benchmark like that: https://github.com/awulkiew/benchmark-geometry/blob/master/benchmarks/algorithms.cpp#L35.   
  
You could also write simple loop testing similarity of geometries containing N and M points K times. These numbers should be sufficiently big that the test would take several seconds. E.g.:  
  
    #include <chrono>  
  
    struct benchmark_clock  
    {  
        typedef std::conditional  
            <  
                std::chrono::high_resolution_clock::is_steady,  
                std::chrono::high_resolution_clock,  
                std::chrono::steady_clock  
            >::type clock_t;  
	    typedef clock_t::time_point time_point_t;  
        typedef clock_t::duration duration_t;  
  
        benchmark_clock()  
            : start(clock_t::now())  
        {}  
  
        double seconds() const  
        {  
            time_point_t now = clock_t::now();  
            duration_t elapsed = now - start;  
            return std::chrono::duration_cast<std::chrono::milliseconds>(elapsed).count() / 1000.0;  
        }  
  
        time_point_t start;  
    };  
  
    const size_t K = 10000; // example value  
  
    int main()  
    {  
        // all required definitions here  
        // create geometries etc.  
  
        {  
            benchmark_clock clk;  
            for (size_t i = 0 ; i < K ; ++i)  
            {  
                // call your algorithm for one data set  
            }  
            double s = clk.seconds();  
            std::cout << s << "s" << std::endl;  
        }  
    }  
  
In case the compiler optimized the loop out just store some result of computation e.g. sum of distances from all iterations (e.g. `foo += hausdorff_distance(...);`) and then print this value or dump somewhere else after the time is assigned.  
  
In both cases C++11 is required but this is not a problem.

---

## Review 24 [Commented]

> Username: awulkiew  
> Created_at: 2018-07-09 12:13:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-135389718  

📁 include/boost/geometry/algorithms/hausdorff_distance.hpp

```diff
 141 |+         
 142 |+         boost::geometry::detail::throw_on_empty_input(r1);
 143 |+         boost::geometry::detail::throw_on_empty_input(r2);
```

> Username: awulkiew  
> Created_at: 2018-07-09 12:13:11 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r200979288  

Note that everything above from this point in the struct is the same for rtree and loop variants. To not duplicate the code you could enable only the code below with `#ifdef BOOST_GEOMETRY_ENABLE_SIMILARITY_RTREE` not the whole struct.  
  
Even more flexible would be implementing the loop and rtree search in a separate struct and take it as Policy (`range_range` template argument defining `apply()` static member function) and pick this Policy later in `dispatch::hausdorff_distance` definition for two linestrings.

> Username: awulkiew  
> Created_at: 2018-07-09 12:18:21 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r200980688  

And then you could call the Policy with a loop in `point_range` directly. This would also fix the problem that `point_range` checks the size of a linestring each time it's called and this is not needed since it's already checked here.

> Username: yaghya  
> Created_at: 2018-07-10 09:53:20 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r201282981  

merged the previous 2 struct ( range_range with rtree and with loop ) in single struct using         
`#ifdef BOOST_GEOMETRY_ENABLE_SIMILARITY_RTREE` condition.


---

## Review 25 [Commented]

> Username: awulkiew  
> Created_at: 2018-07-09 12:18:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-135391495  

📁 include/boost/geometry/algorithms/hausdorff_distance.hpp

```diff
  57 |+         typedef typename boost::range_size<Range>:: type size_type;
  58 |+         
  59 |+         boost::geometry::detail::throw_on_empty_input(pnt);
```

> Username: awulkiew  
> Created_at: 2018-07-09 12:18:56 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r200980846  

This is not needed because point cannot be empty in Boost.Geometry.

> Username: yaghya  
> Created_at: 2018-07-10 09:50:17 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r201282017  

Removed in the updated code.


---

## Review 26 [Commented]

> Username: awulkiew  
> Created_at: 2018-07-09 12:20:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-135391799  

📁 include/boost/geometry/algorithms/hausdorff_distance.hpp

```diff
  58 |+         
  59 |+         boost::geometry::detail::throw_on_empty_input(pnt);
  60 |+         boost::geometry::detail::throw_on_empty_input(rng);
```

> Username: awulkiew  
> Created_at: 2018-07-09 12:20:00 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r200981099  

Right, so if this function is called below in `range_range` this check is performed in each iteration of the higher-level loop. It would be good to avoid it.

> Username: yaghya  
> Created_at: 2018-07-10 09:50:06 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r201281966  

Removed in the updated code.


---

## Comment 27

> Username: yaghya  
> Created_at: 2018-07-11 19:07:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-404278117  

I tried different test cases and got the following results using the simple loop for testing similarity that you provided ,  
MULTIPOINT          
N=101,M=101,K=10000          
With R-tree Time = 17.026s,19.358s,17.684s,18.088s   
With Loop Time = 37.452s,39.666s,36.964s,37.699s  
  
  
LINESTRING  
N=101,M=101,K=10000          
With R-tree Time = 16.61s,18.303s,18.994s,16.762s  
With Loop Time = 37.473s,38.747s,37.581s,36.56s  
  
  
MULTIPOINT          
N=51,M=51,K=10000          
With R-tree Time = 8.145s,8.164s,8.047s,8.08s  
With Loop Time = 11.294s,11.422s,11.222s,11.1s  
  
  
LINESTRING  
N=51,M=51,K=10000          
With R-tree Time = 7.737s,7.919s,8.243s,7.73s  
With Loop Time = 11.048s,10.868s,10.706s,10.763s  
  
  
MULTIPOINT  
N=21,M=21,K=10000  
With R-tree Time = 3.485s,3.109s,3.108s,3.104s  
With Loop Time = 2.835s,2.702s,2.691s,2.764s  
  
LINESTRING  
N=21,M=21,K=10000  
With R-tree Time = 3.019s,3.021s,2.963s,2.996s  
With Loop Time = 2.587s,2.55s,2.583s,2.576s         
  
LINESTRING  
N=11,M=11,K=10000  
With R-tree Time = 1.401s,1.382s,1.399s,1.355s  
With Loop Time = 1.071s,1.103s,1.16s,1.11s  
  
MULTIPOINT  
N=11,M=11,K=10000  
With R-tree Time = 1.454s,1.459s,1.459s,1.545s  
With Loop Tree = 1.19s,1.14s,1.135s,1.118s  
  
MULTILINESTRING  
N=20,M=20,K=10000 //each linestring of N*M and 3 such linestrings in each MultiLinestring  
With R-tree Time = 18.389s,18.457s,18.454s  
With Loop Time = 14.891s,15.298s,14.709s  
  
MULTILINESTRING  
N=50,M=50,K=10000 //each linestring of N*M and 3 such linestrings in each MultiLinestring  
With R-tree Time = 51.806s,51.406s,51.569s  
With Loop Time = 84.628s,83.771s,78.191s  
  
MULTILINESTRING  
N=100,M=100,K=100 //each linestring of N*M and 4 linestrings in each  MultiLinestring  
With R-tree Time = 2.045s,2.061s,2.062s  
With Loop Time = 5.183s,5.176s,5.174s  
  
each time result with R-tree is corresponding to the time result with Loop below it  .

---

## Comment 28

> Username: awulkiew  
> Created_at: 2018-07-12 01:35:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-404361663  

How did you generate the geometries? Are the points random? If that's the case then AFAIU this is the best scenario for the R-tree since the loop-based solution should break early if the geometries were similar. But randomized geometries won't be similar. I suggest to test randomized (or as different as possible) v.s. the same v.s. partially similar geometries.  
  
How did you pick the numbers of points? E.g. where 21 comes from? I think three orders of magnitude are sufficient 10, 100, 1000. Plus maybe if you have some free time an example from the real world like maybe some relatively complex chemical compounds or some biological structures like different kinds of spider webs as multilinestring. Something you find interesting which would look cool on the final report.

---

## Comment 29

> Username: yaghya  
> Created_at: 2018-07-12 06:56:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-404409324  

I used a python script to generate the geometries and yes the points are random.  
Ok I will test the test randomized  v.s. the same v.s. partially similar geometries and will update you , shortly .I set the  number of points  in the geometry through the python script and  I will look for some example from the real world and run the similarity test over them.

---

## Comment 30

> Username: yaghya  
> Created_at: 2018-07-18 18:16:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-406026148  

I was looking into the idea of extending the Hausdorff distance to continuous Hausdorff distance by Instead of traversing geometries point by point do this segment by segment and pick the distance , as you have mentioned in the earlier comments. Should I start implementing it, or do you want me to focus on something else. And sorry for not writing earlier, as I was traveling for the past few days.

---

## Comment 31

> Username: awulkiew  
> Created_at: 2018-07-21 00:04:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-406754602  

For now I'd like to know the results of performance testing with R-tree for the same geometries v.s. partially similar v.s. totally different. To know what are the times in the best and worst case scenarios.

---

## Comment 32

> Username: yaghya  
> Created_at: 2018-07-21 21:13:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-406823910  

These are the results for the same geometries v.s. partially similar v.s. totally different geometries for Linestring and MultiPoint  
  
  
LINESTRING  
N=10 ; M=10 ; K=10000  
For Same Geometries  
With R-tree => 1.308s,1.298s,1.3s,1.293s  
With loop   => 0.943s,0.945s,0.946s,0.949s  
For Partially Similar Geometries  
With R-tree => 1.248s,1.238s,1.31s,1.327s  
With loop   => 0.944s,0.938s,0.94s,0.935s  
For Totally different Geometries  
With R-tree => 1.267s,1.309s,1.38s,1.366s  
With loop   => 0.961s,0.989s,0.975s,0.974s  
  
N=100 ; M=100 ; K=10000  
For Same Geometries  
With R-tree => 16.038s,15.624s,16.43s,15.715s  
With loop   => 36.867s,38.378s,37.371s,36.381s  
For Partially Similar Geometries  
With R-tree => 16.084s,16.206s,16.01s,16.249s  
With loop   => 36.331s,36.166s,35.917s,35.737s  
For Totally different Geometries  
With R-tree => 17.268s,17.402s,16.768s,17.661s  
With loop   => 37.29s,36.402s,35.813s,37.542s  
  
N=1000 ; M=1000 ; K=100  
For Same Geometries  
With R-tree => 1.901s,1.884s,1.878s,1.919s  
With loop   => 31.886s,31.619s,31.298s,31.159s  
For Partially Similar Geometries  
With R-tree => 2.045s,2.21s,2.232s,2.157s  
With loop   => 31.477s,32.059s,32.375s,32.178s  
For Totally different Geometries  
With R-tree => 2.055s,1.892s,2.048s,2.053s  
With loop   => 31.641s,33.744s,33.546s,32.275s  
  
MULTIPOINT  
N=10 ; M=10 ; K=10000  
For Same Geometries  
With R-tree => 1.301s,1.275s,1.275s,1.278s  
With loop   => 1.135s,1.118s,1.111s,1.201s  
For Partially Similar Geometries  
With R-tree => 1.413s,1.424s,1.386s,1.3s  
With loop   => 1.016s,1.008,1.004s,1.038s  
For Totally different Geometries  
With R-tree => 1.322s,1.312s,1.353s,1.369s  
With loop   => 1.066s,1.119s,1.128s,1.132s  
  
N=100 ; M=100 ; K=10000  
For Same Geometries  
With R-tree => 15.676s,15.85s,15.883s,15.769s   
With loop   => 36.45s,36.429s,36.495s,36.68s  
For Partially Similar Geometries  
With R-tree => 18.704s,18.601s,17.818s,17.889s  
With loop   => 38.26s,37.451s,36.484s,37.511s  
For Totally different Geometries  
With R-tree => 16.72s,17.235s,17.3s,17.104s  
With loop   => 36.364s,36.454s,36.675s,36.445s  
  
N=1000 ; M=1000 ; K=100  
For Same Geometries  
With R-tree => 1.951s,1.946s,1.944s,1.97s   
With loop   => 30.97s,30.99s,30.923s,30.933s  
For Partially Similar Geometries  
With R-tree => 2.02s,2.02s,2.041s,2.134s  
With loop   => 31.062s,31.047s,31.553s,31.096s  
For Totally different Geometries  
With R-tree => 2.115s,1.943s,2.102s,2.115s  
With loop   => 31.159s,30.986s,31.464s,31.444s

---

## Comment 33

> Username: awulkiew  
> Created_at: 2018-07-24 13:51:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-407413781  

Interesting. It seems that the change in data doesn't cause the change in performance. So I guess we could see at which point it's beneficial to switch from loop to R-tree and use one or the other for some number of points. This will probably depend on the actual hardware but we could use some rough esstimate, e.g. 10.  
  
Furthermore the geometry for which you create the R-tree should probably be the one with lesser number of points because the complexity is roughly NlogN+MlogN (where N is the number of elements in the R-tree) so the resulting complexity is the least if N < M I think. You could check this if you like by creating a test case of e.g. 10 element v.s. 1000 element linestrings passed as first and second parameters and then in reversed order.  
  
Are the points contained by the MultiPoint and Linestring different or the same? The performance should be the same because the same function is used right? But I see that the performance is different.  
  
Regarding the next steps in addition to the ones mentioned above (so e.g. as you mentioned implementing contineous hausdorff distance) you could also prepare the existing algorithms for the release so write the documentation.  
  
You could indeed implement contineous hausdorff distance but now that I think about it it wouldn't be that simple. It wouldn't be sufficient to simply replace Pt-Pt with Seg-Seg strategy. It wouldn't be sufficient to find the closest segments. You'd need to have a strategy returning the furthest distance between segments but existing distance strategy returns the closest one. @vissarion @mkaravel correct me if I'm wrong.  
  
Consider this example:  
  
    LINESTRING(0 0, 10 10) v.s. LINESTRING(0 0, 10 0, 10 10)  
  
for both segments the Seg-Seg distance strategy would return distance 0, but the hausdorff distance AFAIU should be `5sqrt(2)`, right? At least it would be this for:  
  
    LINESTRING(0 0, 5 5, 10 10) v.s. LINESTRING(0 0, 10 0, 10 10)  
  
So if I'm correct in order to implement contineous hausdorff distance we' have to implement specific contineous hausdorff distance strategies returning the furthest distance between two segments.  
  
So it's up to you, do you want to learn how to implement new strategies or how to document algorithms in Boost.Geometry?

---

## Comment 34

> Username: yaghya  
> Created_at: 2018-07-24 16:56:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-407477409  

I can do a binary search between 10 and 100, to get a good estimate at which we can switch between loop and R-tree.  
  
I tried the test over  10 element v.s. 1000 element Linestrings passed as first and second parameters and then in reversed order for R-tree:-  
M=10;N=1000;K=1000  
time => 6.611s, 6.608s  
M=1000; N=10; K=1000  
time=> 8.179s, 8.775s  
  
Yes, the points contained by the MultiPoint and Linestring are the same and also for both of them same function is used. I observed that when the algorithm is run over the same input two times, the performance is very close but not exactly the equal. This might be the reason why the performance is different in the case of Linestring and MultiPoint.  
  
Yes for the Hausdorff distance we need a strategy that uses the furthest distance between segments. And @awulkiew you are right, for both the Linestrings example the Hausdorff distance will be 5sqrt(2), but the Seg-Seg distance strategy will return 0.  
  
And I would like to learn how to document algorithms for Boost.Geometry and prepare the existing algorithms for the release.

---

## Comment 35

> Username: awulkiew  
> Created_at: 2018-07-25 13:14:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-407749726  

Ok so let's focus on the documentation for now.  
  
Boost.Geometry is using doxygen format and injects QuickBook to generate the docs.  
  
Read: [Generating and Improving Documentation](https://github.com/boostorg/geometry/wiki/Generating-and-Improving-Documentation) and learn how to build documentation.  
  
Here is a PR adding new algorithm `densify()` with the documentation: https://github.com/boostorg/geometry/pull/437  
You can see there what changes needs to be done in the `doc/` directory.  
And here is the result:  
- [densify](https://www.boost.org/doc/libs/1_67_0/libs/geometry/doc/html/geometry/reference/algorithms/densify/densify_3.html)  
- [densify (with strategy)](https://www.boost.org/doc/libs/1_67_0/libs/geometry/doc/html/geometry/reference/algorithms/densify/densify_4_with_strategy.html)  
  
We should also change the names of functions to explicitly state what's the algorithm and avoid confusion. `discrete_hausdorff_distance` and `discrete_frechet_distance` are the most straightforward so you could these. Unless you have a better idea? @vissarion @mkaravel do you have any suggestions?

---

## Comment 36

> Username: vissarion  
> Created_at: 2018-07-26 07:57:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-408010879  

@awulkiew I agree that Pt-Pt distance strategy does not suffice for the continuous Hausdorff but I do not see how Seg-Seg furthest distance would help.   
In your example    
```LINESTRING(0 0, 10 10) v.s. LINESTRING(0 0, 10 0, 10 10)```  
the furthest distance between e.g. `SEGMENT(0 0, 10 10)` and `SEGMENT(0 0,10 0)` will be `10` not `5\sqrt(2)`.   
  
I would say that Pt-Seg distance is the right strategy (and already implemented). That is, for each point compute the closest distance to the other geometry (uses Pt-Seg strategy), do this for both geometries and take the maximum. This is brute force and can be optimized (e.g. using R-tree).  
  
@yaghya your work using the R-tree looks great!

---

## Comment 37

> Username: awulkiew  
> Created_at: 2018-07-26 12:09:33 UTC  
> Updated_at: 2018-07-31 00:34:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-408075937  

@vissarion Currect, I was not precise enough. The furthest point as defined by the hausdorff distance between all points of both segments (interpolated from the endpoints). So the maximum distance between the closest points on both segments. It looks like it'd always be one of the endpoints because this is the point which can be the furthest one, at least in cartesian CS and for short spherical and geographic segments. Long spherical and geographic segments are a different story. Is that correct? Does it mean that in cartesian we only need Pt-Seg distance strategy and call it 4 times (all endpoint v.s. the other segment)?

---

## Comment 38

> Username: vissarion  
> Created_at: 2018-07-27 07:04:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-408331268  

>@vissarion Currect, I was not precise enough. The furthest point as defined by the hausdorff distance between all points of both segments (interpolated from the endpoints). So the maximum distance between the closest points on both segments. It looks like it'd always be one of the endpoints because this is the point which can be the furthest one, at least in cartesian CS and for short spherical and geographic segments. Long spherical and geographic segments are a different story. Is that correct? Does it mean that in sartesian we only need Pt-Seg distance strategy and call it 4 times (all endpoint v.s. the other segment)?  
  
This is correct (this is also how shortest distance between geometries is already implemented in Boost Geometry). I would also say this is also true for segments of any length on the ellipsoid. At least I do not see an example where the shortest distance between two segments is realized as the distance between two points none of which is an endpoint.

---

## Comment 39

> Username: awulkiew  
> Created_at: 2018-07-27 13:05:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-408412578  

Right, but here we want to calculate the max shortest distance between points so in case of geodesics this could be the distance between the vertex of the geodesic and the other segment. Consider e.g. one segment going through the north pole (edge case) and the other one shorter (e.g. 1 degree short) slightly below the pole with geodesic vertex on the meridian perpendicular to the long segment (dividing the long segment on 2 halves, so shorter segment "symmetrically" below) then the shortest distance between them will indeed be the distance between either of the endpoints of the shorter segment and the one going through the pole. However AFAIU the max shortest distance would be the distance between geodecis' vertices in this case.

---

## Comment 40

> Username: yaghya  
> Created_at: 2018-08-02 15:15:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-409963027  

I tried to add documentation for discrete_frechet_distance and discrete_hausdorff_distance. I have made all the required changes in the `doc/` directory , according the changes made in the PR #437 for the `densify()` algorithm. I am getting this output when I tried to build the documentation using  [Generating and Improving Documentation ](https://github.com/boostorg/geometry/wiki/Generating-and-Improving-Documentation) ,  
```  
Boost.Geometry is making .qbk files in /home/yaghya/Music/boost_1_67_0/libs/geometry/doc  
Boost.Geometry is cleaning Doxygen files in /home/yaghya/Music/boost_1_67_0/libs/geometry/doc/doxy/doxygen_output/xml/  
Traceback (most recent call last):  
  File "/home/yaghya/Music/boost_1_67_0/libs/geometry/doc/make_qbk.py", line 202, in <module>  
    exec(open("make_qbk.py").read())  
  File "<string>", line 33, in <module>  
  File "<string>", line 22, in run_command  
Exception: Error running doxygen_xml2qbk --xml xml/classboost_1_1geometry_1_1index_1_1rtree.xml --start_include boost/ --output_style alt > generated/rtree.qbk  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
      
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
    PYTHONPATH="/home/yaghya/Music/boost_1_67_0/libs/geometry/doc"  
export PYTHONPATH  
 "/home/yaghya/Documents/Anaconda/bin/python3.6" "/home/yaghya/Music/boost_1_67_0/libs/geometry/doc/make_qbk.py" --release-build --doxygen-xml2qbk "../../../bin.v2/libs/geometry/doc/src/docutils/tools/doxygen_xml2qbk/gcc-5.4.0/debug/link-static/doxygen_xml2qbk" > "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug/make_qbk.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug/make_qbk.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug/make_qbk.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug/make_qbk.output" "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug/make_qbk"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug/make_qbk.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed capture-output ../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug/make_qbk...  
...skipped <p../../../bin.v2/libs/geometry/doc/gcc-5.4.0/debug>geometry.boostbook for lack of <p../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-5.4.0/debug>make_qbk.test...  
...failed updating 1 target...  
...skipped 2 targets...  
```  
can you please help me in resolving this.

---

## Comment 41

> Username: yaghya  
> Created_at: 2018-08-04 10:40:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-410440698  

I was able to resolve this error and also able to generate discrete_frechet_distance.qbk and discrete_hausdorff_distance.qbk in the `BoostRoot/libs/geometry/doc/generated/` directory. And this is the output of discrete_frechet_distance.qbk  
```  
[/============================================================================  
  Boost.Geometry (aka GGL, Generic Geometry Library)  
  
  Copyright (c) 2007-2013 Barend Gehrels, Amsterdam, the Netherlands.  
  Copyright (c) 2008-2013 Bruno Lalande, Paris, France.  
  Copyright (c) 2009-2013 Mateusz Loskot, London, UK.  
  
  Use, modification and distribution is subject to the Boost Software License,  
  Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at  
  http://www.boost.org/LICENSE_1_0.txt)  
=============================================================================/]  
  
  
[/ Generated by doxygen_xml2qbk 1.1.1, don't change, will be overwritten automatically]  
[/ Generated from doxy/doxygen_output/xml/group__discrete__frechet__distance.xml]  
```  
Can you please confirm, is the output correct? As no documentation is generated in the `BOOST_ROOT/libs/geometry/doc/html` directory.

---

## Review 42 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-04 12:06:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-143381063  

📁 doc/doxy/doxygen_input/groups/groups.hpp

```diff
  35 | \defgroup difference difference: difference of two geometries
  36 |+ \defgroup discrete_frechet_distance discrete_frechet_distance: calculate discrete frechet distance between two geometries
  37 |+ \defgroup discrete_hausdorff_distance discrete_hausdorff_distance: calculate discrete hausdorff distance between two geometries
```

> Username: awulkiew  
> Created_at: 2018-08-04 12:06:19 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r207707115  

I propose to add one group `similarity` and use it in both cases.


---

## Review 43 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-04 12:07:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-143381073  

📁 doc/quickref.xml

```diff
 398 |      <member><link linkend="geometry.reference.algorithms.densify">densify</link></member>
 399 |     </simplelist>
 400 |+     <bridgehead renderas="sect3">Discrete_Frechet_Distance</bridgehead>
```

> Username: awulkiew  
> Created_at: 2018-08-04 12:07:13 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r207707128  

Here too, I propose to have only one header `Similarity`.


---

## Comment 44

> Username: awulkiew  
> Created_at: 2018-08-04 12:39:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-410446734  

> Can you please confirm, is the output correct? As no documentation is generated in the BOOST_ROOT/libs/geometry/doc/html directory.  
  
Is my understanding correct that the docs are generated but your functions are not there in the reference? And to be more precise there are sections you added but the pages are empty?  
  
Maybe I wasn't clear enough, sorry. In addition to the changes in doc directory you have done you have to add doxygen-style documentation for functions in hpp files in the first place. Based on this doxygen generates xml with the description which is then processed by `doxygen_xml2qbk` tool. Here it is defined for `densify()`, 2 variants, with and without strategy:  
https://github.com/boostorg/geometry/pull/437/files#diff-f2979e36206b16ff7fb0a77593a86ff6R350  
https://github.com/boostorg/geometry/pull/437/files#diff-f2979e36206b16ff7fb0a77593a86ff6R397  
  
Later you should also add an example. In order to do that you have to:  
- write cpp file with comments which can be understand by QuickBook:  
  https://github.com/boostorg/geometry/pull/437/files#diff-99260881a98d4c4d22e4c483ab73e3e3R11  
  https://github.com/boostorg/geometry/pull/437/files#diff-99260881a98d4c4d22e4c483ab73e3e3R38  
- add it to Jamfile for testing (this does not affect docs generation):  
  https://github.com/boostorg/geometry/pull/437/files#diff-4fbfc5123fde769a5e72caf3e2bfb123R44  
- add this file to `import.qbk`:  
  https://github.com/boostorg/geometry/pull/437/files#diff-61241b6a9bb8a89e642c7610c5efe5a6R33  
- put sections in the doxygen comments in hpp files:  
  https://github.com/boostorg/geometry/pull/437/files#diff-f2979e36206b16ff7fb0a77593a86ff6R370  
  https://github.com/boostorg/geometry/pull/437/files#diff-f2979e36206b16ff7fb0a77593a86ff6R370

---

## Comment 45

> Username: yaghya  
> Created_at: 2018-08-07 22:25:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-411222892  

I have added the documentation for the two Similarity algorithms. If you want any modifications then please let me know.  
I was thinking of sharing this PR link in the Final Evaluation form, in the Work Product section. Also if you have any suggestion for doing the Final Evaluation, then please let me know.

---

## Review 46 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-09 15:34:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-144900133  

📁 doc/quickref.xml

```diff
 405 |-     <simplelist type="vert" columns="1">
 406 |-      <member><link linkend="geometry.reference.algorithms.discrete_hausdorff_distance">discrete_hausdorff_distance</link></member>
 407 |-     </simplelist>
```

> Username: awulkiew  
> Created_at: 2018-08-09 15:34:33 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r208977286  

You should leave both algorithms but use only one title, like here:  
https://github.com/boostorg/geometry/blob/develop/doc/quickref.xml#L326


---

## Review 47 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-09 15:35:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-144900609  

📁 doc/reference.qbk

```diff
 189 |+ [include generated/similarity.qbk]
 190 |+ [endsect]
 191 |+ 
```

> Username: awulkiew  
> Created_at: 2018-08-09 15:35:32 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r208977678  

This doesn't work does it? Anyway, in the reference you should have both algorithms like it was before.


---

## Review 48 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-09 15:35:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-144900667  

📁 doc/reference.qbk

```diff
 118 |- [section:discrete_hausdorff_distance discrete_hausdorff_distance]
 119 |- [include generated/discrete_hausdorff_distance.qbk]
 120 |- [endsect]
```

> Username: awulkiew  
> Created_at: 2018-08-09 15:35:39 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r208977722  

This was ok.


---

## Review 49 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-09 15:40:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-144902692  

📁 doc/reference/algorithms/discrete_hausdorff_distance.qbk

```diff
  18 |- [note The units of the distance depends on strategy. In order to change the
  19 |- default behavior a user has to create a strategy and pass it explicitly into
  20 |- the algorithm.]
```

> Username: awulkiew  
> Created_at: 2018-08-09 15:40:02 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r208979341  

I'm fairly sure that this should stay and that such description should be there for both algorithms.

> Username: yaghya  
> Created_at: 2018-08-12 09:28:26 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209449041  

I have kept this in the updated documentation for both the algorithms.


---

## Comment 50

> Username: awulkiew  
> Created_at: 2018-08-09 16:59:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-411826742  

I was wrong regarding the group. It seems that based on doxygen group a reference page containing versions of algorithm (with and without strategy) is generated. So unfortunately you need 2 groups one for each algorithm.  
  
The rest still holds. You need doxygen comments in hpp files with injected qbk sections for each version of each algorithm.

---

## Comment 51

> Username: yaghya  
> Created_at: 2018-08-11 23:41:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412308490  

I have made two groups "Discrete_frechet_distance" and "Discrete_hausdorff_distance" and also made all the required changes in the documentation. If there are any modifications required then please let me know.

---

## Comment 52

> Username: yaghya  
> Created_at: 2018-08-12 21:53:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412374352  

I have also updated the project page https://boostgsoc18.github.io/Similarity-Between-Geometries/  where I have docmented the work I have done.

---

## Review 53 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 11:02:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-145604882  

📁 doc/quickref.xml

```diff
 405 |+     <simplelist type="vert" columns="1">
 406 |+      <member><link linkend="geometry.reference.algorithms.discrete_hausdorff_distance">discrete_hausdorff_distance</link></member>
 407 |+     </simplelist>
```

> Username: awulkiew  
> Created_at: 2018-08-13 11:02:42 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209568935  

Here you could have them both under one header:  
  
    <bridgehead renderas="sect3">Similarity</bridgehead>  
    <simplelist type="vert" columns="1">  
      <member><link linkend="geometry.reference.algorithms.discrete_frechet_distance">discrete_frechet_distance</link></member>  
      <member><link linkend="geometry.reference.algorithms.discrete_hausdorff_distance">discrete_hausdorff_distance</link></member>  
    </simplelist>  
  
The easiest thing to do would be to place it just before **Simplify**, in the same table column (https://www.boost.org/doc/libs/1_68_0/libs/geometry/doc/html/geometry/matrix.html).


---

## Review 54 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 11:06:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-145605945  

📁 include/boost/geometry/algorithms/discrete_frechet_distance.hpp

```diff
 175 |+ [/discrete_frechet_distance_output]
 176 |+ }
 177 |+ */
```

> Username: awulkiew  
> Created_at: 2018-08-13 11:06:19 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209569743  

Great, it's almost done. You also need the same-style description above the other variant of the algorithm taking Strategy with `\qbk{distinguish,with strategy}`.

> Username: awulkiew  
> Created_at: 2018-08-13 11:19:19 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209572995  

Regarding the *Available Strategies* you could take them from here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/distance/interface.hpp#L340  
  
you only need:  
  
    \* [link geometry.reference.strategies.strategy_distance_pythagoras Pythagoras (cartesian)]  
    \* [link geometry.reference.strategies.strategy_distance_haversine Haversine (spherical)]  
  
Just have in mind that you need to have a newline between *Available strategies* and *Examples* like here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/densify.hpp#L369


---

## Review 55 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 11:07:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-145606201  

📁 include/boost/geometry/algorithms/discrete_frechet_distance.hpp

```diff
 155 |         >::type
 156 |- frechet_distance(Geometry1 const& g1, Geometry2 const& g2, Strategy const& strategy)
 156 |+ frechet_distance(Geometry1 const& geometry1, Geometry2 const& geometry2, Strategy const& strategy)
```

> Username: awulkiew  
> Created_at: 2018-08-13 11:07:15 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209569959  

Here, the description is missing. Also the functions should be called the same as the file so `discrete_frechet_distance()`.


---

## Review 56 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 11:07:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-145606435  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 264 |         >::type
 265 |- hausdorff_distance(Geometry1 const& g1, Geometry2 const& g2, Strategy const& strategy)
 265 |+ hausdorff_distance(Geometry1 const& geometry1, Geometry2 const& geometry2, Strategy const& strategy)
```

> Username: awulkiew  
> Created_at: 2018-08-13 11:07:55 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209570168  

Missing doxygen documentation and old function name.


---

## Review 57 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 11:09:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-145606836  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 269 | 
 270 | // Algorithm overload using default Pt-Pt distance strategy
 271 |+ /*!
```

> Username: awulkiew  
> Created_at: 2018-08-13 11:09:11 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209570577  

The line above this comment  
  
    // Algorithm overload using default Pt-Pt distance strategy  
  
is not needed.

> Username: yaghya  
> Created_at: 2018-08-13 11:38:34 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209577432  

ok.


---

## Comment 58

> Username: awulkiew  
> Created_at: 2018-08-13 12:05:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412495828  

Great, now only change the names of functions to `discrete_frechet_distance` and `discrete_hausdorff_distance` and I think this PR will be ready for merging.  
  
@barendgehrels @vissarion @mloskot what do you think?  
  
If others agree you'll be able to write in the final report that your work has been included in the library.

---

## Comment 59

> Username: mloskot  
> Created_at: 2018-08-13 12:56:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412508417  

It's already renamed, isn't it?

---

## Comment 60

> Username: awulkiew  
> Created_at: 2018-08-13 15:51:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412566443  

@mloskot I see that the functions are called `frechet_distance` and `hausdorff_distance`:  
  -  include/boost/geometry/algorithms/discrete_frechet_distance.hpp, line 204: (https://github.com/boostorg/geometry/pull/490/files#diff-5dbd6ec57faef4a9b6e9d3664e3dabd7R204)  
  - include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp, line 312: (https://github.com/boostorg/geometry/pull/490/files#diff-bb2403b707e1aa8595602effa12bcaa0R312)  
  
You can also see this by clicking "Show outdated" link in the above comments where the argument names were changed.  
  
Or am I missing something?

---

## Comment 61

> Username: yaghya  
> Created_at: 2018-08-13 20:42:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412657036  

@awulkiew should I write in the final report that my work has been included in the library?

---

## Comment 62

> Username: mloskot  
> Created_at: 2018-08-13 20:45:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412657867  

@awulkiew It seems I got confused by the addition to the quickbook doc:  
  
```  
+[section:discrete_frechet_distance discrete_frechet_distance]  
  
+[include generated/discrete_frechet_distance.qbk]  
  
+[endsect]  
  
+  
  
+[section:discrete_hausdorff_distance discrete_hausdorff_distance]  
  
+[include generated/discrete_hausdorff_distance.qbk]  
  
+[endsect]  
```

---

## Comment 63

> Username: awulkiew  
> Created_at: 2018-08-13 21:51:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412677464  

@yaghya Only if you make the necessary changes and everybody agrees.  
  
@mloskot Yes, I haven't checked but I think the documentation cannot be generated properly right now.

---

## Comment 64

> Username: yaghya  
> Created_at: 2018-08-14 11:08:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412836135  

I have added strategies in the documentation and made all the required changes.

---

## Review 65 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 12:15:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146034044  

📁 doc/make_qbk.py

```diff
 200 | os.chdir("index")
 201 |- exec(open("make_qbk.py").read())
 201 |+ execfile("make_qbk.py")
```

> Username: awulkiew  
> Created_at: 2018-08-14 12:15:12 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209927557  

Why have you reverted it back?

> Username: yaghya  
> Created_at: 2018-08-14 12:18:22 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r209928523  

I was using Python3 so I changed that and I revert it to make it as same as before.


---

## Comment 66

> Username: awulkiew  
> Created_at: 2018-08-14 16:06:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412926084  

Great, now that the documentation can be built you only have to fix unit tests and examples. :)

---

## Review 67 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:09:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146139663  

📁 doc/src/examples/algorithms/discrete_frechet_distance.cpp

```diff
  20 |+     boost::geometry::read_wkt("LINESTRING(0 0,1 1,1 2,2 1,2 2)", linestring1);
  21 |+     boost::geometry::read_wkt("LINESTRING(1 0,0 1,1 1,2 1,3 1)", linestring2);    
  22 |+     typename distance_result<typename point_type<point_type>::type,typename point_type<linestring_type>::type>::type res;
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:09:49 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210012441  

Namespace missing. But since your point type is `double` you can simply use `double` here. It's an example and it should be simple:  
  
    double res = ...


---

## Review 68 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:10:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146139809  

📁 doc/src/examples/algorithms/discrete_hausdorff_distance.cpp

```diff
  21 |+     boost::geometry::read_wkt("LINESTRING(1 0,0 1,1 1,2 1,3 1)", linestring2);    
  22 |+     typename distance_result<typename point_type<point_type>::type,typename point_type<linestring_type>::type>::type res;
  23 |+     res = boost::geometry::hausdorff_distance(linestring1,linestring2);
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:10:09 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210012570  

double res = ...


---

## Review 69 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:11:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146140196  

📁 test/algorithms/Jamfile.v2

```diff
  63 |     ;
  64 | 
  65 |+ build-project similarity;
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:11:10 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210012886  

Projects should be listed in alphabetical order.


---

## Review 70 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:11:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146140366  

📁 test/algorithms/similarity/Jamfile.v2

```diff
  12 |+     :
  13 |+     [ run discrete_hausdorff_distance.cpp                     : : : : algorithms_discrete_hausdorff_distance ]
  14 |+     [ run discrete_frechet_distance.cpp                       : : : : algorithms_discrete_frechet_distance ]
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:11:33 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210013032  

`f` < `h`


---

## Review 71 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:12:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146140583  

📁 test/algorithms/similarity/discrete_frechet_distance.cpp

```diff
  85 |+ }
  86 |+ 
  87 |+ int main()
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:12:03 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210013198  

`int test_main(int, char* [])`


---

## Review 72 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:12:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146140955  

📁 test/algorithms/test_frechet_distance.hpp

```diff
  19 |+ #include <boost/geometry/strategies/strategies.hpp>
  20 |+ #include <boost/variant/variant.hpp>
  21 |+ #include <boost/test/test_tools.hpp>
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:12:58 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210013494  

This header (`boost/test/test_tools.hpp`) is not needed.


---

## Review 73 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:13:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146141132  

📁 test/algorithms/test_frechet_distance.hpp

```diff
  14 |+ #define BOOST_GEOMETRY_TEST_DEBUG
  15 |+ 
  16 |+ 
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:13:18 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210013640  

Header (`#include <geometry_test_common.hpp>`) is missing.


---

## Review 74 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:13:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146141255  

📁 test/algorithms/test_hausdorff_distance.hpp

```diff
  17 |+ #include <boost/geometry/strategies/strategies.hpp>
  18 |+ #include <boost/variant/variant.hpp>
  19 |+ #include <boost/test/test_tools.hpp>
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:13:35 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210013753  

This header is not needed.


---

## Review 75 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:14:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146141475  

📁 test/algorithms/similarity/discrete_hausdorff_distance.cpp

```diff
  11 |+ #include <vector>
  12 |+ 
  13 |+ #define BOOST_GEOMETRY_TEST_DEBUG
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:14:04 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210013933  

This should not be defined in the cpp file.


---

## Review 76 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:15:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146141927  

📁 test/algorithms/similarity/discrete_frechet_distance.cpp

```diff
  11 |+ #include <vector>
  12 |+ 
  13 |+ #define BOOST_GEOMETRY_TEST_DEBUG
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:15:18 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210014292  

This should not be defined in the cpp file. This is not a part of the standard testing. You should enable it in the compiler if needed.


---

## Review 77 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:15:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146142061  

📁 test/algorithms/similarity/discrete_hausdorff_distance.cpp

```diff
  25 |+ void test_all_cartesian()
  26 |+ {
  27 |+ 	typedef model::linestring<P> linestring_2d;
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:15:36 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210014402  

Wrong indentation.


---

## Review 78 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:16:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146142253  

📁 test/algorithms/test_frechet_distance.hpp

```diff
  12 |+ #define BOOST_GEOMETRY_TEST_frechet_DISTANCE_HPP
  13 |+ 
  14 |+ #define BOOST_GEOMETRY_TEST_DEBUG
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:16:03 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210014554  

This should be be defined in the hpp file.


---

## Review 79 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:16:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146142353  

📁 test/algorithms/test_frechet_distance.hpp

```diff
  10 |+ 
  11 |+ #ifndef BOOST_GEOMETRY_TEST_frechet_DISTANCE_HPP
  12 |+ #define BOOST_GEOMETRY_TEST_frechet_DISTANCE_HPP
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:16:18 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210014630  

Only upper-case words.


---

## Review 80 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:20:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146144071  

📁 test/algorithms/similarity/discrete_frechet_distance.cpp

```diff
  20 |+ #include <boost/geometry/geometries/polygon.hpp>
  21 |+ 
  22 |+ using namespace boost::geometry;
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:20:41 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210016031  

Typically we're defining a namespace alias and using that instead:  
  
    namespace bg = boost::geometry;  
  
and then:  
  
    typedef bg::model::linestring<P> linestring_2d;

> Username: awulkiew  
> Created_at: 2018-08-14 16:22:37 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210016656  

But you don't really have to define this alias because it's already done in `geometry_test_common.hpp`. So this line is not needed.


---

## Review 81 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-14 16:22:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146144997  

📁 test/algorithms/similarity/discrete_hausdorff_distance.cpp

```diff
  20 |+ #include <boost/geometry/geometries/polygon.hpp>
  21 |+ 
  22 |+ using namespace boost::geometry;
```

> Username: awulkiew  
> Created_at: 2018-08-14 16:22:58 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210016761  

Remove, use `bg::` instead.


---

## Comment 82

> Username: awulkiew  
> Created_at: 2018-08-14 16:34:02 UTC  
> Updated_at: 2018-08-14 16:35:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-412935075  

In addition to the comments above the frechet distance cases are failing with gcc-5.5 and clang-3.8. For coordinate types `float` and `int` the result is different than expected probably due to the fact that you computed it for `double`, e.g.:  
  
    difference{1.02932e-05} between h_distance{333961.438} and expected_frechet_distance{333958} exceeds 0.001%  
    difference{0.00114489} between h_distance{0.05235987755982989} and expected_frechet_distance{0.052299999999999999} exceeds 0.001%  
  
So either test coordinate types separately (with different expected results) or only test `double` (remove `int` and `float`) or hide them behind `#ifdef`. I'm ok with testing only `double`. Note also that testing `int` in geographic and spherical doesn't have sense when degrees or radians are represented directly.  
  
Unless there is something wrong with the algorithm. And in this case the algorithm must be fixed.  
  
In the future make sure that the code works before pushing it.

---

## Comment 83

> Username: awulkiew  
> Created_at: 2018-08-15 13:07:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-413191801  

Great, thanks!  
  
Many wierd indentations appeared in the test files. It looks like spaces were automatically changed to tabs or something. Is it on purpose? You should use 4-space indentations for Boost.Geometry.  
  
There is some conflict with develop probably after my change in tests. Do you know how to resolve it?  
  
    git checkout develop  
    git pull  
    git checkout feature/similarity  
    git merge develop  
    git push  
  
should be enough.

---

## Review 84 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-15 15:26:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146493607  

📁 test/algorithms/Jamfile.v2

```diff
  80 | build-project relate ;
  81 | build-project set_operations ;
  82 |+ build-project similarity;
```

> Username: awulkiew  
> Created_at: 2018-08-15 15:26:31 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210307131  

One more thing. A space between word `similarity` and `;` is needed.


---

## Review 85 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-15 15:48:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-146503076  

📁 doc/src/examples/algorithms/discrete_frechet_distance.cpp

```diff
  21 |+     boost::geometry::read_wkt("LINESTRING(1 0,0 1,1 1,2 1,3 1)", linestring2);    
  22 |+  	double res;
  23 |+     res = boost::geometry::frechet_distance(linestring1,linestrign2);
```

> Username: awulkiew  
> Created_at: 2018-08-15 15:48:44 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r210314830  

Wrong function name.


---

## Comment 86

> Username: awulkiew  
> Created_at: 2018-08-15 15:52:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-413242254  

Ok, please let me know when you make sure that everything compiles and runs properly with `b2`, so when run in `BOOST_ROOT\libs\geometry`:  
  
    ../../b2 test/algorithms  
    ../../b2 doc/src/examples/algorithms

---

## Comment 87

> Username: yaghya  
> Created_at: 2018-08-15 20:44:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-413330034  

I have Updated the examples and Jamfile. And now both  
```  
../../b2 test/algorithms  
../../b2 doc/src/examples/algorithms  
```  
runs properly .

---

## Comment 88

> Username: awulkiew  
> Created_at: 2018-08-16 13:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-413538812  

It works for me as well. Good job.  
  
I think this PR is good enough to be merged. What do you think @barendgehrels @mloskot @vissarion ?

---

## Review 89 [Commented]

> Username: vissarion  
> Created_at: 2018-08-21 08:24:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-147950122  

Thanks, great job! I have few minor comments. I am ok with merging.

📁 include/boost/geometry/algorithms/discrete_frechet_distance.hpp

```diff
 164 |+ \* [link geometry.reference.strategies.strategy_distance_pythagoras Pythagoras (cartesian)]
 165 |+ \* [link geometry.reference.strategies.strategy_distance_haversine Haversine (spherical)]
 166 |+ 
```

> Username: vissarion  
> Created_at: 2018-08-21 08:20:25 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r211516811  

There are also geographic strategies available right?

> Username: yaghya  
> Created_at: 2018-08-26 15:20:22 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r212829754  

For the Geographic Coordinate system, only the default strategy works. I have not tested the code with vincenty and andoyer strategy yet.

> Username: awulkiew  
> Created_at: 2018-08-26 15:40:55 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r212830275  

What do you mean by that? The algorithm doesn't compile? The result is incorrect? Or documentation cannot be built?  
  
Right now geographic strategies are not mentioned at all in the documentation I think. So if we wanted to add link here we should first document geographic Pt-Pt distance strategy.

> Username: vissarion  
> Created_at: 2018-08-27 06:56:22 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r212880924  

@yaghya It would be good to add some tests using the other strategies too. `andoyer` is the default so I guess you have to test `vincenty` and `thomas`.   
  
Regarding documentation what about adding text (without link) to notify the user that geographic strategies can be used even if they are currently undocumented.

> Username: awulkiew  
> Created_at: 2018-08-27 12:38:16 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r212958907  

If you decide to add more tests use:  
  
    strategy::distance::geographic<strategy::andoyer>()  
    strategy::distance::geographic<strategy::thomas>()  
    strategy::distance::geographic<strategy::vincenty>()  
  
Regarding documentation I think it's a bad idea. If we agree that the current interface for geographic CS will not change the documentation has to be updated consistently. In that case all geographic strategies, srs spheroid and geographic policies (`andoyer`, `thomas`, `vincenty`) has to be documented and links to strategies added in every algorithm, not only here.

> Username: yaghya  
> Created_at: 2018-08-30 17:54:32 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r214124157  

Added test cases Geographic Strategies and Updated the documentation.

> Username: awulkiew  
> Created_at: 2018-08-30 19:38:33 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r214156001  

This change only makes the docs more confusing, not less.

---

📁 include/boost/geometry/algorithms/discrete_frechet_distance.hpp

```diff
 148 |+ 
 149 |+ /*!
 150 |+ \brief calculate discrete frechet distance between two geometries using specified strategy
```

> Username: vissarion  
> Created_at: 2018-08-21 08:21:21 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r211517050  

It should be useful to state that current implementation only support certain types of geometries, i.e. linestrings.


📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 258 |+ 
 259 |+ /*!
 260 |+ \brief calculate discrete hasudorff distance between two geometries using specified strategy
```

> Username: vissarion  
> Created_at: 2018-08-21 08:22:48 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r211517506  

Similar to Frechet distance useful to state that current implementation only support certain types of geometries and geographic strategies are available.


---

## Review 90 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-30 19:39:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-151151281  

📁 include/boost/geometry/algorithms/discrete_frechet_distance.hpp

```diff
 194 |- \qbk{[include reference/algorithms/discrete_frechet_distance.qbk]}
 195 |+ \qbk{[inc\* more (currently extensions): Vincenty\, Andoyer (geographic)
 196 |+ lude reference/algorithms/discrete_frechet_distance.qbk]}
```

> Username: awulkiew  
> Created_at: 2018-08-30 19:39:15 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r214156187  

I doubt the docs can be built now.


---

## Review 91 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-30 19:40:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-151151596  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 259 | /*!
 260 |- \brief calculate discrete hausdorff distance between two geometries ( currently works for LineString-LineString, MultiPoint-MultiPoint, Point-MultiPoint, MultiLineString-MultiLineString ) using specified strategy
 260 |+ \brief calculate discrete hasudorff distance between two geometries ( currently works for LineString-LineString, MultiPoint-MultiPoint, Point-MultiPoint, MultiLineString-MultiLineString ) using specified strategy
```

> Username: awulkiew  
> Created_at: 2018-08-30 19:40:13 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r214156473  

?


---

## Review 92 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-30 19:41:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/490#pullrequestreview-151152036  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 271 | 
 272 |- \qbk{
 272 |+  \qbk{
```

> Username: awulkiew  
> Created_at: 2018-08-30 19:41:43 UTC  
> Updated_at: 2018-08-30 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#discussion_r214156836  

Why is this space needed?


---

## Comment 93

> Username: awulkiew  
> Created_at: 2018-08-30 19:48:40 UTC  
> Updated_at: 2018-08-30 19:52:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-417445200  

FYI, I'm waiting with merging this PR because I want to hear from @barendgehrels. Not because I expect you to improve it. Well, at least this was the case before the recent changes because now it's no longer ready to be merged.

---

## Comment 94

> Username: yaghya  
> Created_at: 2018-08-30 21:35:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-417474832  

Sorry for the silly mistakes. I have Updated the doc and now both  
```  
../../b2 test/algorithms  
../../b2 doc/src/examples/algorithms  
```  
runs properly.

---

## Comment 95

> Username: awulkiew  
> Created_at: 2018-09-14 21:35:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-421490650  

Ok, we waited long enough. I'm merging the PR. Thanks for your contribution!

---

## Comment 96

> Username: barendgehrels  
> Created_at: 2018-09-15 12:49:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-421565448  

Sure, sorry @awulkiew  , I was on holiday at the time you announced to wait for me.  
@yaghya thanks a lot for the new algorithm!   
And thanks for merging.  
Regards, Barend

---

## Comment 97

> Username: awulkiew  
> Created_at: 2018-09-16 08:52:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-421726232  

Ok, thanks!

---

## Comment 98

> Username: DingQinbin  
> Created_at: 2020-10-22 09:02:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-714346155  

> Implemented Frechet distance and Hausdorff distance algorithms for Linestrings. Checked the algorithms for different Coordinate System like Cartesian , Geographic and Spherical_Equatorial.  
  
Can you tell me the difference between frechetdistance and hausdorff distance and the application scenarios of the two methods in the direct words

---

## Comment 99

> Username: awulkiew  
> Created_at: 2020-10-22 13:11:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/490#issuecomment-714483670  

@DingQinbin This is not the place for questions like that. The internet is vast and has the information you need.

---
