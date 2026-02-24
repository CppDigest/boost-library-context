# #650 Missing input combinations in intersection() and introduction of tupled-output. [Merged]

> Username: awulkiew  
> Created at: 2019-11-27 19:52:36 UTC  
> Updated at: 2020-03-26 14:13:02 UTC  
> Merged at: 2020-03-26 14:13:02 UTC  
> Closed at: 2020-03-26 14:13:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/650  

In this PR I add implementation for missing input combinations of `intersection` (1) and enhance the existing interface allowing to pass multiple output geometries at once (2). An alternative adding more output arguments to intersection would also be possible (3).  
  
There are also some issues with intersection which I'd prefer not to address in this PR (4).  
  
### 1. Missing input combinations  
  
Added implementation for:  
* PointLike ∩ Areal  
  
### 2. Enhanced tupled-output interface  
  
Added implementation for:  
* PointLike ∩ PointLike - requires pair/tuple at least containing MultiPoint  
* PointLike ∩ Linear - requires pair/tuple at least containing MultiPoint  
* PointLike ∩ Areal- requires pair/tuple at least containing MultiPoint  
* Linear ∩ Linear - requires pair/tuple at least containing MultiPoint and MultiLinestring  
* Linear ∩ Areal - requires pair/tuple at least containing MultiPoint and MultiLinestring  
* Areal ∩ Areal - requires tuple at least containing MultiPoint, MultiLinestring and MultiPolygon  
  
Actually what is required are Ranges of Points, Linestrings or Polygons.  
  
#### 2.1. The problem  
  
Consider two linestrings:  
  
    A = LINESTRING(0 0, 1 0, 1 1, 2 0, 3 0)  
    B = LINESTRING(0 0, 1 0, 2 0, 3 1)  
  
The result of the intersection is a linestring and a point:  
  
    A ∩ B = LINESTRING(0 0, 1 0) ∪ POINT(2 0)  
  
![image](https://user-images.githubusercontent.com/1226951/69752005-94386080-1150-11ea-8acd-9c098d5495b7.png)  
  
The function however is able to only take a range of linestrings (or multi-linestring) as output geometry. So the result of the operation for the data above is:  
  
    A ∩ B = MULTILINESTRING((0 0, 1 0), (2 0, 2 0))  
  
with the point represented as degenerated linestring. This forces the user to either ignore it and potentially get wrong results from other operations after passing this multi-linestring or to traverse the result and filter-out the degenerated linestrings.  
  
It's more complex than that because for different combinations of input geometries different results are generated. E.g. for Linear ∩ Areal -> Linear the points are ignored, so not even represented as degenerated lineatrings. Furthermore for Linear ∩ Areal -> PointLike the result contains all intersection points. So without 2 separate calls and further analysis of the outputs it is impossible to calcualte the correct result of the intersection of Linear ∩ Areal.  
  
#### 2.2. The solution  
  
Allow the user to optionally pass a collection of output geometries. I decided to allow `std::pair` and `std::tuple` (the latter conditionally) because they are the standard and `boost::tuple` because we already depend on it and at least for now still support C++03. This is how the code looks like:  
  
    linestring a{ {0, 0}, {1, 0}, {1, 1}, {2, 0}, {3, 0} };  
    linestring b{ {0, 0}, {1, 0}, {2, 0}, {3, 1} };  
    std::tuple<multi_point, multi_linestring> result;  
    bg::intersection(a, b, result);  
  
#### 2.3. The implementation  
  
Internally we pass single-geometry extracted from multi-geometry as template argument and output iterator of multi-geometry into `intersection_insert` algorithm which then appends the resulting single-geometries to the output using the output iterator and returns this iterator. So it more or less follows the interface of STL algorithms. I decided to not change that (at least for now\*). So what is done in the `intersection` is:  
  - a tuple type of single-geometries is created from tuple of ranges/multi-geometries  
  - a tuple of `back_insert_iterator`s is created from tuple of ranges/multi-geometries  
  - they are passed into the `intersection_insert`  
  - internally the implementation for linestrings is able to distinguish between the old behavior (`GeometryOut` as linestring) or the optional behavior (`GeometryOut` as tuple) and uses the types and objects accordingly.  
  - this is done only if tuples of geometries were not already adapted to any geometry concept by the user in order to not break the backward compatibility  
  
\* In general I think it would be good to simplify and redesign set operations slightly. The interfaces could be made more clear (e.g. the behavior not changing based on output). I'd deprecate some of the combinations which doesn't make sense or at least moved to a separate algorithm. Like returning the intersection points, unless I'm missing what's their purpose. Going through `intersection_insert` from all operations is also confusing. Different operations can have different outputs even for the same input (e.g. difference of linear geometries can not generate points but the intersection can). Internally they call `intersection_insert` with the same interface for both. But redesigning set operations is not the intention of this PR.  
  
### 3. Enhanced interface taking more arguments.  
  
An alternative to (2) could be the interface taking more than one output geometry. For example if the input was two polygons the user would be forced to pass three ranges for pointlike, linear and areal portions of the output (plus optional strategy). E.g.:  
  
    polygon a{...};  
    polygon b{...};  
    multi_point res_p;  
    multi_linestring res_l;  
    multi_polygon res_a;  
    bg::intersection(a, b, res_p, res_l, res_a);  
  
I'm not sure whether or not it's more clear because we also need to pass optional strategy so all of these arguments would be optional which may be not clear for the user.  
  
### 4. The issues  
  
For various combinations unnecessary points can be returned. These are points that are intersecting other portions of the output and can be generated e.g. for touching exterior and interior rings or linear rings touching at the endpoints. This is the old behavior of the intersection and I didn't change that. In order to address this issue we'd have to modify L/L and P/L implementation to check the resulting points whether or not they should really be returned. But this is the existing behavior which I don't want to change in this PR.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-12-05 15:06:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/650#pullrequestreview-327618748  

I am in general ok with this interface. The only drawback I see is that the user has to know the types of the geometries for the output tuple which is different for each pair of input geometry types. However, if this intended for advanced users maybe it's ok since there is also the simple interface.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-12-05 15:43:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#issuecomment-562186073  

@vissarion It would be possible to always pass all of them and the algorithm will pick the right ones. The assertion in the `intersection_insert` for linestrings checks the minimal requirement. So it would be possible to pass e.g. `std::tuple<multi_point, multi_linestring, multi_polygon>` into all operations and based on the operation get correct geometries filled.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-12-11 14:12:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#issuecomment-564560204  

Interesting PR - I wanted to check it today, but have to postpone it again a bit. Will look soon. Thanks.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-01-19 18:29:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#issuecomment-576032455  

I'm going to add support for all missing combinations in `intersection()` and then ask you for a review.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-01-23 15:15:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#issuecomment-577725528  

@vissarion @barendgehrels Ok, I think the PR is ready for review. I don't plan to add anything more for now.

---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2020-02-10 14:17:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/650#pullrequestreview-355884802  

Thanks for this PR. I am ok with merging.

📁 include/boost/geometry/algorithms/detail/intersection/areal_areal.hpp

```diff
  61 |+ };
  62 |+ 
  63 |+ // TODO: Ideally this should be done in one call of intersection_insert
```

> Username: vissarion  
> Created_at: 2020-02-10 12:03:59 UTC  
> Updated_at: 2020-03-24 13:43:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#discussion_r377022379  

What is missing for this? Is more work on ` intersection_insert` needed?

> Username: awulkiew  
> Created_at: 2020-02-10 14:43:38 UTC  
> Updated_at: 2020-03-24 13:43:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#discussion_r377104254  

This implementation for Areal/Areal calls `intersection_insert` for other combinations several times. This means that partition is called and then turns are analysed several times. However, overlay stuff called by the old/basic Areal/Areal implementation (when the output is MultiPolygon instead of tuple) has all of the information needed to generate the result in one call, so to generate Areal + Linear + PointLike output.  
  
I decided to not modify it mainly because it could interfere with @barendgehrels work on rescaling. The code is also more complex than in case of Linear geometries, with enriching turns, storing states in turns and visitors which are not clear to me. So I'd prefer to leave it to @barendgehrels or do it when he's done with rescaling. Maybe rewrite it, maybe for C++11/14, etc. In short, it was simpler this way.


---

## Comment 7

> Username: awulkiew  
> Created_at: 2020-02-12 17:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#issuecomment-585333433  

@barendgehrels what do you think about it?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2020-03-24 12:11:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#issuecomment-603203025  

@barendgehrels the release is comming. Master branch will be closed on Apr 1 for major changes like this one. What should I do with this PR?

---

## Review 9 [Approved]

> Username: barendgehrels  
> Created_at: 2020-03-25 09:26:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/650#pullrequestreview-380971513  

>   
>   
> @barendgehrels the release is comming. Master branch will be closed on Apr 1 for major changes like this one. What should I do with this PR?  
  
Thanks for the heads up, sorry for the delay.  
  
It looks very good in general! I did not dive into all the details, but I'm OK with merging it, if you're confident that it is all fine.

📁 include/boost/geometry/algorithms/detail/intersection/areal_areal.hpp

```diff
  35 |+                         >::type
  36 |+ >
  37 |+ struct intersection_areal_areal_
```

> Username: barendgehrels  
> Created_at: 2020-03-25 09:18:55 UTC  
> Updated_at: 2020-03-25 09:26:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#discussion_r397706698  

why the trailing `_` ?

> Username: awulkiew  
> Created_at: 2020-03-25 17:00:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#discussion_r398017654  

Because below the name `intersection_areal_areal` was already used. The original one. And I needed to distinguish between the original behavior and the handling of Tuples. Would you prefer a different name?

---

📁 include/boost/geometry/algorithms/detail/intersection/areal_areal.hpp

```diff
 115 |+ 
 116 |+         // NOTE: The same robust_policy is used in each call of
 117 |+         //   intersection_insert. Is that correct?
```

> Username: barendgehrels  
> Created_at: 2020-03-25 09:19:29 UTC  
> Updated_at: 2020-03-25 09:26:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#discussion_r397707014  

Yes, and hopefully we will get rid of it...


---

## Comment 10

> Username: awulkiew  
> Created_at: 2020-03-25 17:01:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/650#issuecomment-603962638  

@barendgehrels Ok, thanks!

---
