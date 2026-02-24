# #901 fix: small fixes to support boost multiprecision and rational [Closed]

> Username: barendgehrels  
> Created at: 2021-08-18 13:52:20 UTC  
> Updated at: 2021-09-01 08:22:00 UTC  
> Closed at: 2021-09-01 08:21:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/901  

Because of the first version of previous PR (errors in rational), I added a small unit test for this type and Boost multi precision. There were some small fixes necessary, mainly in new functions.  
  
The new test case looks like this and is supported and correct (even using boost rational).  
  
![rectangular](https://user-images.githubusercontent.com/334849/129909792-ad4dd10e-84e2-4433-9ca5-a028f8f19aa4.png)  
  
The first test (the simplex) was incorrect for boost rational, and give different results with or without my other PR (because of diagonals), so I left that out for now).  
  
![multi_simplex](https://user-images.githubusercontent.com/334849/129910205-672d1bc1-5f0a-4c5a-81f7-726411e299e5.png)

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:46:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/901#pullrequestreview-736024176  

📁 include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp

```diff
 319 |-         double const tolerance = 1.0e9;
 319 |+         using ct_type = typename geometry::coordinate_type<Point>::type;
 320 |+         ct_type const tolerance = 1000 * 1000 * 1000;
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:46:43 UTC  
> Updated_at: 2021-08-23 11:49:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#discussion_r693899343  

This will probably not compile properly on platforms with 16-bit `int` becasue there will be an overflow. We probably don't target such platforms but still. Also I'm not fully certain that this will be the case. What was the reason behind this change?  
  
EDIT: To be sure it could be changed to:  
```  
ct_type const tolerance = ct_type(1000) * ct_type(1000) * ct_type(1000);  
```

> Username: vissarion  
> Created_at: 2021-08-31 11:44:12 UTC  
> Updated_at: 2021-08-31 11:45:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#discussion_r699241894  

I also not understand this change. Is this to support multiprecision? Why not `ct_type const tolerance = 1000000000` ?

> Username: barendgehrels  
> Created_at: 2021-09-01 08:19:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#discussion_r699989732  

I'll fix it.  
  
Because I'm now working in two branches which are quite related to each other, and they are both approved, I will decline this PR and move this commit to the other PR.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:53:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/901#pullrequestreview-736029305  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 787 |-         it != boost::end(sections);
 788 |-         ++it)
 786 |+     for (auto& section : sections)
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:53:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#discussion_r693903450  

Can we be certain that `sections` defines `begin()` and `end()` member functions? Is this a standard container? Becasue if this is a type adapted to Boost.Range concept then range-based for loop may not compile for it.

> Username: barendgehrels  
> Created_at: 2021-09-01 08:19:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#discussion_r699989872  

I'll check.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:56:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/901#pullrequestreview-736031792  

📁 test/string_from_type.hpp

```diff
  50 |- template <> struct string_from_type<boost::multiprecision::cpp_bin_float_100>
  50 |+ template <typename Backend>
  51 |+ struct string_from_type<boost::multiprecision::number<Backend>>
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:56:21 UTC  
> Updated_at: 2021-08-23 11:57:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#discussion_r693905454  

`Backend` can define various floating point and integral types. Is this specialization correct for all of them or should it be narrowed to floating point types only?  
  
EDIT: Ah ok, this is probably only for debugging.

> Username: barendgehrels  
> Created_at: 2021-09-01 08:20:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#discussion_r699990517  

Yes, it's for debug. It's used for display on screen and in svg filenames. It's OK if they all are indicated with `m`


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2021-08-31 11:45:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/901#pullrequestreview-742618234  

OK with merging, thanks!

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2021-09-01 08:21:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/901#issuecomment-910056633  

Closing, the code will appear in #897

---
