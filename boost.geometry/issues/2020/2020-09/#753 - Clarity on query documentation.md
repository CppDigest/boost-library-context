# #753 - Clarity on query documentation [Closed]

> Username: jpan127  
> Created at: 2020-09-14 23:13:27 UTC  
> Updated at: 2021-09-28 22:43:27 UTC  
> Closed at: 2021-09-28 22:43:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/753  

Hi, this might be already obvious but I would like a bit of clarity on the documentation of the query iterators.  
  
>[note In case of k-NN queries performed with `query()` function it's not guaranteed that the returned values will be sorted according to the distance.  
      It's different in case of k-NN queries performed with query iterator returned by `qbegin()` function which guarantees the iteration over the closest `__value__`s first. ]  
  
When using the iterators for `nearest`, we can expect guarantees about the output's order.  Does this guarantee strictly apply to only this predicate?  Ideally I would like the same guarantee for `intersects`.  
  
Thanks!

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-09-15 11:45:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692662253  

With query iterators taking `nearest` predicate and any number of other predicates in any configuration (`intersects`, `satisfies`, etc...) the iterated elements are sorted by distance.  
  
With `query` member function taking `nearest` predicate and any number of other predicates the elements that are returned are `k` nearest elements that are not sorted by distance. So they are sorted WRT all of the other elements in the r-tree because these are the `k` nearest elements but not WRT each other.  
  
Is that explanation clear?

---

## Comment 2

> Username: jpan127  
> Created at: 2020-09-15 12:48:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692691998  

Sorry, I meant to ask for using query `iterators without nearest` but with `intersects`.  I'm guessing the answer is no there is no guarantee 😄.

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-09-15 15:26:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692791750  

Yes, the answer is no. :)  
It's because `intersects` is a spatial predicate and there is no information passed which would allow the elements to be sorted in meaningful way. In other words, the query returns elements occupying some area and none of the elements is more important than the other one.   
  
Unless I'm missing something. Do you have a case where you'd like to sort the elements somehow? An example perhaps?

---

## Comment 4

> Username: jpan127  
> Created at: 2020-09-15 16:03:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692814196  

Thanks! 😄   
  
My use case right now is to find intersection between a point and an RTree of boxes, but have the intersecting boxes be sorted by ascending distance (from point).  I guess the best way is to post-sort them then.

---

## Comment 5

> Username: awulkiew  
> Created at: 2020-09-15 16:23:21 UTC  
> Updated at: 2020-09-15 16:24:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692825692  

In general, yes, if you need all of the objects, not `k` nearest ones, then it'll be the fastest way to perform a spatial query and then sort the resulting elements.  
  
Have in mind that you'd achieve the same outcome if you used query iterator with `intersects` and `nearest` predicates, passing some big `k` (e.g. the number of all elements in the R-tree). Though the performance would be worse than calling `query()`, getting all of the intersecting elements and sorting them afterwards.  
  
In your particular case I don't understand how the sorting would work because if a point intersects a box then the distance from this point to the box is `0`, so you'll not be able to sort the boxes this way.

---

## Comment 6

> Username: jpan127  
> Created at: 2020-09-15 18:02:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692881179  

You're right, sorry I didn't explain properly.  There is an R-Tree of boxes that represent geometries. I am trying to find all geometries that intersect a search box, and is within some distance from a point.  This point is the center of the larger box.  
  
I am achieving this by:  
```c++  
Box search_box = ...;  
double max_distance = ...;  
const auto it = rtree.qbegin(intersects(search_box) && satisfies([max_distance](const std::pair<Box, Object>& pair) {  
  return distance(pair.second, point) < max_distance;  
}));  
  
// Unsorted  
std::vector<Object> objects;  
for (; it != rtree.qend(); ++id)  
{  
  objects.push_back(it->second);  
}  
  
// Then sort by recomputing distances from point to Object  
```  
  
I'm guessing this is the fastest way?

---

## Comment 7

> Username: awulkiew  
> Created at: 2020-09-15 19:35:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692933390  

Ok, so I see you're storing some Objects in the R-tree and then using query iterator in order to get all of the elements of the query at once.  
  
Depending how big the Object is it is ok to store them in the R-tree or not. It's because Values stored in the R-tree can be copied during `insert()`, tree creation in general or when values are returned by the `query()` method. So if Objects are e.g. polygons it would be better to store them in e.g. a separate container and then in the R-tree store pairs of bounding boxes of polygons with IDs of polygons (index in a vector, iterator in a set, etc.). You could use Boost.Range adaptors to avoid intermediate step of creating a container of R-tree elements before passing them into the R-tree constructor [1] and to create a range of pairs from a container of Objects on the fly.  
  
The second thing is that if you want to get all of the objects in the intersecting region (e.g. you do not want to break the query somewhere in the middle) then it would be faster to call `rtree.query()` (also assuming you're not storing big Objects to avoid copies). You could also use `boost::function_output_iterator` [2] with `query()` in order to avoid the intermediate step of creating a container of R-tree elements which typically could be created with e.g. `std::back_insert_iterator`.  
  
So it'd be something like this (without Boost.Range adaptors and `boost::function_output_iterator`):  
```  
std::vector<Object> objects;  
std::vector<std::pair<Box, size_t>> values;  
for(size_t i = 0 ; i < objects.size() ; ++i)  
    values.push_back(std::make_pair(return_envelope<Box>(objects[i]), i));  
rtree<std::pair<Box, size_t>, rstar<4>> rt(values.begin(), values.end());  
  
// ...  
  
std::vector<std::pair<Box, size_t>> result;  
rt.query(intersects(search_box)  
      && satisfies([&](auto const& pair) {  
            return distance(objects[pair.second], point) < max_distance;  
         }, std::back_inserter(result));  
  
```  
  
[1] https://www.boost.org/doc/libs/1_74_0/libs/geometry/doc/html/geometry/spatial_indexes/rtree_examples/range_adaptors.html  
[2] https://www.boost.org/doc/libs/1_74_0/libs/iterator/doc/function_output_iterator.html

---

## Comment 8

> Username: jpan127  
> Created at: 2020-09-15 19:43:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/753#issuecomment-692937630  

Thanks! `Object` is really an `ObjectPtr` so the copies are less of an issue. I am already doing something similar to what you just described.  
  
Good to know `query` is unequivocally faster than the `iterators` when no exit logic is needed, that makes sense.
