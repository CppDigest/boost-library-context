# #219 Fix point_type traits for variant over MPL sequence [Merged]

> Username: sdebionne  
> Created at: 2015-02-11 16:14:33 UTC  
> Updated at: 2015-03-09 15:36:36 UTC  
> Merged at: 2015-02-19 16:37:50 UTC  
> Closed at: 2015-02-19 16:37:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/219  

When variant types are generated from MPL sequences the actual variant  
type is `boost::variant<over_sequence<Sequence...`  
and thus T0 is `over_sequence<>`. This fix use the first bounded type `boost::variant<>::types`  
instead of T0.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-02-11 23:30:12 UTC  
> Updated_at: 2015-02-12 08:32:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/219#discussion_r24544998  

Although we are not 100% consistent about this, external (to Boost.Geometry) libraries used in Boost.Geometry code are qualified with `boost::`. So here I would expect `boost::variant` (basically as it was before).

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-02-11 23:31:02 UTC  
> Updated_at: 2015-02-12 08:32:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/219#discussion_r24545060  

Same here: `mpl` should be `boost::mpl`.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-02-11 23:31:15 UTC  
> Updated_at: 2015-02-12 08:32:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/219#discussion_r24545066  

Same here for `variant`

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-02-12 00:03:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/219#issuecomment-73992028  

Thanks for catching this! So AFAIU this version should work for any wrapper used internally in `boost::variant<>`.  
  
Btw, does it make sense to check all point types to see if the dimensions and coordinate systems are the same or somehow compatible? Or to pick the one with the most precise coordinate type? Or something similar.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2015-02-12 09:09:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/219#issuecomment-74038952  

@mkaravel The missing `boost::` namespaces are fixed.  
  
@awulkiew Yes this version should work for any wrapper used internally in boost::variant<> since it uses the public interface `variant<>::types` to get the list of bounded types. I did not check if that fix should be applied somewhere else. I guess @brunolalande would know better.  
  
As for your second question, AFAIU the (variant) `point_type` traits is mostly used to compute the return type of (variant) algorithms. See for instance the `length` algorithm. More complex algorithm, such as `distance`, use a `distance_result` metafunction that computes a variant return type according to the input variant bounded types. In the same vein, an algorithm like `length` could return a variant with say an mp float number or a double (`variant<gmp:float, double>`) depending on the input coordinate types. That would be the most flexible solution, but that may not be worth the trouble... Maybe, for `length`,  checking if all the coordinate types are convertible to double would be enough.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-02-12 15:11:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/219#issuecomment-74086230  

@sdebionne Actually I had something else in mind. For instance we could store in a variant geometries using different coordinate types. E.g. the first could be a point defined by floats and the second one a polygon defined by doubles. And such variant would be passed into some algorithm internally using a point_type. In such case a better point_type would be the polygon's. Anyway it's just a thought. I don't think we should expect such cases.  
  
I'm in favor of merging but we should probably wait for a while to give the others the time to comment.

---
