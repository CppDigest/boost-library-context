# #539 - Proposal: Adding the specializations difference<box,box,collection<box>>, sym_difference<box,box,collection<box>> [Open]

> Username: tinko92  
> Created at: 2018-12-10 23:25:40 UTC  
> Updated at: 2019-01-08 19:02:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/539  

I want to propose adding the specializations difference<box,box,collection<box>> and sym_difference<box,box,collection<box>> to the geometric set operations difference and sym_difference. It is easy to see that the set difference between two boxes is not generally a box but instead may require multiple boxes to cover the resulting set.  
  
### Example:  
Consider the 2D-boxes A=box(point(0,0),point(10,10)) and B=box(point(0,0),point(5,5)). For difference(A,B,result), the result would contain at least two boxes, a valid result consisting of as few boxes as possible and without overlapping boxes would be {box(point(5,0),point(10,5)),box(point(0,5),point(10,10))}.  
  
### Rationale  
To justify the proposal, I'll briefly outline some problems in which I needed the set difference between boxes as a collection of boxes.  
I use Boost.Geometry for a number of problems that involve optimizing placements of boxes (2D and 3D) under a variety of constraints. The need for difference<box, box, collection<box>> came up multiple times, here are a few examples of applications:  
  
1. I have a vector V of n-dimensional boxes and need (as part of a larger problem) to compute the complement of their union as a vector of boxes, so I get a large boxes B that covers the whole space, compute B minus V[0], then the difference of the resulting boxes and V[1] and so on (of course, rtree helps to do that only for boxes that overlap with V[1], V[2], and so on).  
  
2. I need (as part of a much larger problem) to know whom's shadows would touch a given if the y-axis would be a light source, i.e.: for rectangles 1,2,3, given as ((0,2),(1,4)), ((2,1),(3,5)), ((4,3),(5,4)), the rectangle  4 ((6,0),(7,6)) would be touched by the light of the y-axis (for y=0 to 1 and y=5 to 6), by the shadow of 2 (for y=1 to 3 and y=4 to 5) and by the shadow of 3 (for y=3 to 4)). The shadow of 1 is completely blocked off by 2. The solution involves a sweep line that is parallel to the y-axis, moves in x-direction and holds the projection (as 1D-boxes, i.e. intervals) unto itself of every box it passes through and computes the difference of existing projections and the next projection of the upcoming box (which reflects the shadow of the old boxes being blocked off by the upcoming ones).   
Variations of this problem (including a 3D version) keep coming up in my work, all requiring the difference operation.  
  
It would also go a small step towards closing this one: https://github.com/boostorg/geometry/issues/483  
  
### Implementation  
  
I have a draft implementation for "difference" here: https://github.com/tinko92/boost_geometry_difference_demo/blob/master/boost_difference.patch  
  
It computes the difference of two boxes, regardless of dimension as a collection of at most 2*d boxes where d is the dimension of the point type.  
  
Based on difference it would be trivial to add sym_difference (which is just the union of A minus B and B minus A) and union_ (if the user wants the union of two boxes as a collection of non-overlapping boxes, like in the proposed implementation of difference, we could compute union_ as the union of A minus B and {B}).

---

## Comment 1

> Username: barendgehrels  
> Created at: 2018-12-11 20:28:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-446350190  

Thanks, but I don't think it will fit as such in the library. As you say - the difference of A - B (both being box) can be a polygon. But it's not implemented like that. It implements (correct me if I'm wrong) it such that it delivers a collection of output boxes.   
That is as you describe, but these options are unsupported (and unplanned) until now.  
There's an option (?) that is not worked out? (box_or_poly)?

---

## Comment 2

> Username: tinko92  
> Created at: 2018-12-11 23:04:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-446396529  

Yes, it is implemented in this draft such that it delivers a collection of output boxes. It is the intention of this proposal that it can be computed in that way if the function is specifically called such that the given output parameter is a collection with value type box. If the output parameter would be chosen by the caller as a polygon this implementation should not be called.  
  
I understand that it is unsupported and unplanned until now, I am proposing an extension of the existing functionality. In short, my case for this proposal is the following  
  
- this implementation would fill in an unused (and, as far as I can see, not otherwise usable) design space, it would not hide an implementation that computes the difference of two boxes as a polygon, because it can only match a call that has a collection of boxes as the third template parameter, so if a polygon is desired, this proposal should not conflict with that, it is only intended to match a call like difference<box, box, collection<box>>(A, B, out).   
- For that specific combination of parameters (<box, box, collection<box>>) it is an implementation that matches the definition (the result is a representation of the spatial set-theoretic difference of the input geometries) and, I think, it would produce a result that is expected by a user when calling the difference-function with these type parameters.   
- I argue that there are use cases for that function in code that uses Boost.Geometry (I've named two use cases that I implemented myself, there are some other uses in the code base I work on). I understand that the development of Boost.Geometry has started out GIS-oriented but it can be used in other domains. The domain which motivates my use of Boost.Geometry is EDA (2D and 3D problems related to EDA).

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-12-12 02:01:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-446433435  

The result of set operation for boxes would have to be something like a Collection of Boxes, a MultiBox or something like a BoxifiedPolygon. In cartesian it indeed might as well be represented as MultiPolygon (not Polygon) but in geographic or spherical a Box cannot be represented as Polygon because our non-cartesian Geometries has segments represented as geodesics. Boxes however have edges defined by parallels and meridians. However I'd argue that if someone uses Boxes he doesn't want to mix them with Polygon-like Geometries because the main point of using Boxes is to simplify the computation by using the specific properties of Boxes (that you can call less-compare with min and max and you know if something is inside or not).  
  
AFAIR set operations accept collections of geometries, they do not require "proper" Multi-Geometries right now. Is that correct? So we wouldn't have to introduce a new concept. With that said, adding something like a MultiBox is an option. Detecting a range of boxes at compile-time would be doable as well.  
  
In fact internally in the implementation of envelope for non-cartesian Multi-Geometries we're using a collection of boxes and finds maximum longitudal gap between them. Though we probably represent them as collection of longitude 1-d intervals, not n-d boxes. With that said, we could provide `envelope()` for MultiBox (or collection of boxes/geometries) because in non-cartesian coordinate systems it's non-trivial and our users might need that. In fact I know about one such user. ;)  
  
AFAIU we can do whatever we like with Boxes or MultiBoxes or whatever any new Geometry we'd like to add (NSphere, Oriented Box, Frustum, Capsule, etc.) because these concepts are not defined by the OGC standard.  
  
I think at this point the most important question is: could this proposed functionality be useful for some users?

---

## Comment 4

> Username: barendgehrels  
> Created at: 2018-12-12 22:56:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-446775778  

Good points.  
Do you plan to implement intersection (not yet mentioned) and union_ (you mention that - but via difference)?  
Could we maybe add it first to extensions, instead of adding it directly to the core library?  
Most probably you will suggest it is (at least for now) cartesian-only (unless there is really a usecase for spherical/cartesian, which makes it indeed much more complex)?

---

## Comment 5

> Username: awulkiew  
> Created at: 2018-12-13 00:45:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-446800117  

I think it won't be much more complex in spherical, you only have to take winding and poles into account. The boxes are still the same in the coordinates space, i.e. (besides poles treated differently because longitudes doesn't matter there) the result of the intersection is the same as in cartesian. In spherical/geographic after calculating it you only have to normalize the result.  
  
I do not argue to implement it now, just pointing out it wouldn't be hard.

---

## Comment 6

> Username: tinko92  
> Created at: 2018-12-14 01:06:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-447177486  

Thank you both for the feedback.  
  
@barendgehrels I have not thought of implementing intersection for <box, box, collection<box>>. From experience, when I work with box-intersections, I compute the intersection (since it can only be either one box or no box) as one box and check whether it is valid. Since it is not much code, it might make sense to add it to be consistent with the admissible parameters for spatial set operations.   
  
I'm a little unsure about the union_-case. For difference and sym_difference you generally need to create new boxes to represent the resulting set. For union_ one could argue that the union of box A and box B would already be represented by the collection {A, B} but one would not call an algorithm for that. One could also implement it as (A - B) ∪ B or (B - A) ∪ A (maybe whichever has fewer boxes). That would also represent the union but given as a list of (as few as possible) non-overlapping boxes. This is usually what I want, when I want the union of two boxes in a way that requires any computation, but the name of the algorithm is not "union_as_non_overlapping_boxes" so even if a user wants that, I'm not sure, he would look for it in the union_-function. It is much more straightforward with difference and sym_difference. Because of this ambiguity, I did not include a proposal for union_ in my original post.  
  
I'd be happy to write a patch and create a pull request for difference, sym_difference, maybe intersection (since it would be just a few lines) and, depending on further comments on the matter, union_, that would put these in experimental. You are correct in your assumption that I only thought of cartesian coordinates. The implementation does work for arbitrary dimensions, though.  
  
@awulkiew Thanks for the comments on other coordinate systems. I do not have much experience with those but if you say that it would not be much work, I will look into it after finishing the implementation for cartesian coordinates.

---

## Comment 7

> Username: awulkiew  
> Created at: 2018-12-14 02:22:30 UTC  
> Updated at: 2018-12-14 02:23:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-447190783  

> I will look into it after finishing the implementation for cartesian coordinates.  
  
Ok, though it's possible that noone will find a use for it. It might be more beneficial to implement other algorithms for cartesian Boxes. Especially that you said that you're using other set operations too, not only `difference`.  
  
The result of `union_` would depend on the concept of MultiBox or collection of boxes, i.e. what is the valid collection of boxes, should boxes overlap, etc. So it'd have to be defined by us. I'd say that your intuition is probably correct that the result would be a collection of least possible number of non-overlapping boxes. So the name doesn't matter, what matters is the definition of collection boxes used in the whole library. I assume there is one, the best representation and that how a collection of boxes is treated doesn't depend on the context. So e.g. if the MultiBox concept defines that the boxes cannot overlap the user may expect a certain result. Just like the result of a set operation of 2 Polygons is a MultiPolygon which is defined by the OGC standard as a collection of Polygons with non-intersecting interiors that may have rings (boundaries) non-crossing and touching at most in finite number of points. So in MultiPolygons the Polygons touching with edges are not allowed but for MultiBox this would not allow to represent all of the results so boxes would probably have to be able to touch with edges.  
  
Natural extension of your proposal are set operations of MultiBoxes, i.e. algorithms taking two collections of boxes and returning another collection of boxes representing e.g. intersection of the input collections. So even if you don't want to implement such algorithms now the definitions/concepts we create now should take possible extensions into account.  
  
In case of `intersection` I think we have it implemented already (at least for input being Boxes):  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/intersection_box_box.hpp  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/intersection/box_box.hpp  
The result is expected to be a Box right now.  
  
Since you have more experience in working with such collections of boxes. Would it have sense to have other algorithms supporting them as well? Relational operations like `disjoint` or `covered_by`? How about `centroid`? Or measuring algorithms like `area` or `perimeter`? Perimeter would be interesting because it is not clear to me whether parts of edges where boxes are touching should count as boundary or not and therefore be added to perimeter or not. With that said I don't know if it's even needed anyway.  
  
Btw, in case you're interested, when I'm talking about the OGC standard I'm talking about this: https://www.opengeospatial.org/standards/sfa

---

## Comment 8

> Username: tinko92  
> Created at: 2019-01-02 19:43:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-450963946  

I have given the idea of a MultiBox concept some thought and I am a little conflicted. Because the following got a little long-winded, I'll give my conclusion first: I think a MultiBox concept with good algorithm support could be beneficial, but I am unsure how one would accommodate as many users as possible, their use cases and their performance needs. It would probably be a good idea to get more opinions on that.  
  
It would nicely solve the issue of clarifying the expected behavior for various algorithms, as you said. If a MultiBox consists of non-overlapping boxes, then it is clear what to expect from various algorithms. I'd also agree that, when we talk about representing spatial sets with MultiBoxes, it makes sense that the individual boxes do not overlap.  
  
There are a number of algorithms that I would have used in the past for MultiBoxes if such a concept had existed, such as the already mentioned set operations and other algorithms such as area (I'd need to be able to make it return floating point results for integer coordinates, though), append, buffer, clear, correct, covered_by, disjoint, envelope, intersects, is_valid, simplify and within.  
  
But in many cases, the implementation of such algorithms would depend on the data structure. For example, when I want the difference of a MultiBox M minus a Box B and M is some range tree of boxes, I don't need to look at every box in M, I can narrow it down by making a fast query and look only at those that intersect with B.   
In another example, I might have a vector of boxes but keep the boxes sorted by min_corner.x for some plane sweep algorithms and this is a property that could be used to implement some algorithms faster as well (again in the difference example, when I encounter the first box m in M where min_corner.x > B.max_corner.x I can terminate early).  
  
Maybe an implementation could use some code similar to "BOOST_FOREACH(Value & v, rt | bgi::adaptors::queried(bgi::intersects(box_region)))" from the rtree query example. It could default to iterating over all boxes and apply the predicate for simple containers, and it could be overridden for more sophisticated containers (by the developer that adapts his custom data structure to satisfy the MultiBox concept) to make better use of their structure.  
  
When I would want to adapt my data structures to satisfy some Boost MultiBox concept, I would also have to think about non-geometry-related information that is stored alongside the boxes like indices or pointers to some record for which the box is the geometric representation and how algorithms that modify the MultiBox would handle those.   
  
Example: Lets say my Box-type is something like tuple<point,point,record> and the geometry::get and geometry::set methods that I write to satisfy the box concept are only concerned with the first two fields. When I compute (1D-example for simplicity) difference( { {[0 , 10], recordA}, {[12,20], recordB} }, [2,8], output) I probably want the result { {[0,2], recordA}, {[8,10], recordA}, {[12,20], recordB} } and one way to achieve that might be to, for A - B, make copies of A for each of the resulting box and then set the coordinates correctly, assuming it is copyable.  
  
  
If I had to propose a design I'd say: multi_box_tag, behave like Boost.Range Forward Range (Random Access Range would be too restrictive in my opinion*), range_value<...>::type must fulfill box concept, some documentation/specification on how to optionally provide faster implementations for filtering the range with some predicate, like intersects. Provided models: Container<box> (with container defaulting to vector, just like with the provided multi_polygon), maybe also provide a specialization of the rtree as MultiBox-model.  
  
(* what was the rationale for demanding random-access ranges for the existing multi_[geometry]-concepts?)  
  
I'll try to get more opinions from colleagues on use cases and possible data structures for a multi_box concept.  
  
Going back to the original proposal: I think the case for providing set operations for individual boxes is much easier to make. The concepts are already in the library, all use cases I had for set operations on multiple boxes also included use cases for set operations on individual boxes and the ambiguity in the implementation of set operations for individual boxes is more manageable. It might be sensible to allow the user to specify the order of dimensions for slicing up the result into boxes (i.e. whether he wants box(point(0,0),point(10,10)) minus box(point(0,0),point(5,5)) to result in {box(point(5,0),point(10,5)),box(point(0,5),point(10,10))} or {box(point(0,5),point(5,10)),box(point(5,0),point(10,10))}).

---

## Comment 9

> Username: awulkiew  
> Created at: 2019-01-08 19:02:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/539#issuecomment-452413778  

Wow, that's a lot of thoughts. :)  
  
> other algorithms such as area (I'd need to be able to make it return floating point results for integer coordinates, though)  
  
The `result_type` is defined by the Strategy and AFAIR integers are already promoted to `double`.  
  
> simplify  
  
I'm not sure how this would work for boxes?  
  
> (...) is some range tree of boxes, I don't need to look at every box in M, I can narrow it down by making a fast query (...)  
  
Every algorithm in the library works this way, i.e. without the space indexing in place you have to deal with complexity so either do it in linear time or if some algorithm's complexity is quadratic then build temporary index and have roughly linearitmic complexity. Consider e.g. distance(MultiPoint, MultiPolygon). In the future we might implement something like JTS's PreparedGeometry concepts, build and store the index internally with the geometry. Until then we have to deal with what we have.  
  
> In another example, I might have (...)  
  
This is also the case with other algorithms. The user can either use general library solution or implement his own optimized version.  
  
> Maybe an implementation could (...)  
  
The most basic concept should probably have as small number of requirements as possible. In other words it should probably be a RandomAccessRange of Boxes. Do you think that this general solution is not good enough? At least as a starting point.  
  
> (...) I would also have to think about non-geometry-related information that is stored alongside the boxes (...)  
  
Right, this is also a problem for other algorithms like intersection() which can create new points at the intersection points of segments. Currently there is no mechanism allowing the user to define what should happen with additional data in this case. Quick hack would probably be to create such points in some uninitialized state and after the operation manually go through the geometry and initialize uninitialized elements filling non-geometrical data.  
  
> (...) provide a specialization of the rtree as MultiBox-model.  
  
Interesting, AFAIU you'd like to use the indexing features of the rtree like that? Or is it only for convenience and would `rtree` be simply treated as a Range of Boxes? I think that if the performance is really an issue the way to go is rather PreparedGeometry/PreparedMultiBox/etc. with generic interface (though I'm not sure what exactly, this would need a lot of thinking). Then the user would be able to use any space partitioning scheme, R-tree, Kd-tree, sorted vector, etc.  
  
> what was the rationale for demanding random-access ranges for the existing multi_[geometry]-concepts?  
  
I think it's needed in set and relational operations. An algorithm `get_turns` finds intersection points of geometries using space partitioning (algorithm `partition`, kd-tree-like space partitioning) and then these points are used in higher-level algorithm traversing the boundaries of geometries and intersection points when forming new geometries. And this algorithm has to be able to access any segment of any single-geometry of any multi-geometry at any point. It is probably also needed in `buffer()` and relational operations for similar reasons (accessing any geometries from intersection point). Is that correct @barendgehrels?  
  
You don't need random access for MultiBoxes? With that you could create rtree storing IDs of Boxes in MultiBox instead of copying the Boxes to the rtree and in general identify specific Boxes in the algorithm.  
  
Starting with random access iterator would also be safer because you could always relax the requirements (though it would probably never happen).  
  
> I'll try to get more opinions from colleagues on use cases and possible data structures for a multi_box concept.  
  
That's a good idea.  
  
> It might be sensible to allow the user to specify the order of dimensions for slicing up the result (...)  
  
Right, this could be defined in Strategy. As a Policy of the Strategy maybe? A few different Policies come to mind like: minimum area, minimum perimeter, etc.
