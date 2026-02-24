# #1331 chore: change point_type to point_type_t and use 'using' at those places [Merged]

> Username: barendgehrels  
> Created at: 2024-10-13 18:34:31 UTC  
> Updated at: 2024-10-21 13:47:47 UTC  
> Merged at: 2024-10-21 13:47:39 UTC  
> Closed at: 2024-10-21 13:47:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1331  

This PR  
* changes `typename point_type<X>::type` to `point_type_t<X>`  
* at those places, use `using` i/o `typedef`  
* in lines immediately before / after that, also use `using`  
* sometimes also change to `coordinate_type_t` there (but that is not consistently)  
  
I also verified `clang-tidy` with its usage `modernize-use-using` but that is not giving the desired results at this moment.  
  
Clang tidy can, with its option `modernize-use-using`, replace `typedef` with `using. It sometimes works well, but:  
  
* it can replace `typedef differential_quantities<CT, EnableReducedLength, EnableGeodesicScale, 1> quantities` with `using quantities = int` which is plainly wrong  
* it can replace `typedef typename boost::range_value<Range const>::type point_t` with `using point_t = typename boost::range_value<const Range>::type` which moves the const to left, and that is not what is in our conventions  
* it removes any newline and changes neatly formatted long typedefs into a very long one-lined using clause.  
  
Therefore I decided to **not** use it. So this commit changes to `point_type_t`, plus `using`. A next might change to `coordinate_type_t` plus `using` and then we can change the rest manually, or semi-automatically (still using clang-tidy but judge per change)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-13 19:43:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1331#pullrequestreview-2364979703  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 145 |-     static inline auto apply(Range const& rng, Multi_range const& mrng,
 144 |+     template <typename Range, typename MultiRange, typename Strategies>
 145 |+     static inline auto apply(Range const& range, MultiRange const& multi_range,
```

> Username: barendgehrels  
> Created_at: 2024-10-13 19:43:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1331#discussion_r1798526904  

In this file some additional style improvements are done.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2024-10-18 20:15:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1331#issuecomment-2423168537  

It looks good. Thanks.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2024-10-21 09:50:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1331#pullrequestreview-2381508406  

LGTM, thanks!

---
