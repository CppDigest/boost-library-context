# #701 Performance improvements for side_robust [Merged]

> Username: tinko92  
> Created at: 2020-04-22 23:28:50 UTC  
> Updated at: 2020-05-13 13:10:59 UTC  
> Merged at: 2020-05-13 13:10:59 UTC  
> Closed at: 2020-05-13 13:10:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/701  

Hello,  
  
this PR contains some changes for implementation details of side_robust that (on my machine, for the benchmarks that can be found in https://github.com/boostorg/geometry/issues/699#issuecomment-617775379 ) lead to a speedup of ~x1.5 with GCC and ~x4 with Clang in the execution of side_robust::apply for completely random input and a more moderate speedup for degenerate input. Correctness has been verified using the tests linked in the opening post found in https://github.com/boostorg/geometry/issues/699 .

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2020-04-27 09:54:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/701#pullrequestreview-400795526  

I'm OK with merging it into `extensions` after addressing the minor stylistic issues I noted.

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp

```diff
 325 |+                            std::array<RealNumber, 2> const& p3)
 326 |+ {
 327 |+     if(Robustness == 0) {
```

> Username: barendgehrels  
> Created_at: 2020-04-27 09:50:12 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r415671110  

@tinko92 thanks for your benchmark and immediate actions on it!  
I'll just write down some minor things, including stylistic details that we're used to do differently.  
  
This `{` should go to next line

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp

```diff
 326 |+ {
 327 |+     if(Robustness == 0) {
 328 |+         return (p1[0]-p3[0])*(p2[1]-p3[1]) - (p1[1]-p3[1])*(p2[0] - p3[0]);
```

> Username: barendgehrels  
> Created_at: 2020-04-27 09:51:05 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r415671690  

Can you add spaces here between all mathematical operators? `(p1[0] - p3[0]) * `

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp

```diff
 328 |+         return (p1[0]-p3[0])*(p2[1]-p3[1]) - (p1[1]-p3[1])*(p2[0] - p3[0]);
 329 |+     }
 330 |+     std::array<RealNumber, 2> t1, t2, t3, t4;
```

> Username: barendgehrels  
> Created_at: 2020-04-27 09:51:54 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r415672264  

Using this means that we can merge it only in `1.75` as C++03 is then gone.  
Alternatively you can use `boost::array` - but I don't know if more of your code is dependent on C++11

> Username: tinko92  
> Created_at: 2020-04-27 21:01:48 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r416145979  

Would that establish an interface or would later breaking that interface (to use std::array again) be ok because it is in the detail namespace?  
  
I would be fine if it lives in develop/extensions until 1.75 next year. I could also leave it this way and revisit the option to change it to `boost::array` only, if the inclusion of side_robust into the master branch will be considered before 1.74.

> Username: awulkiew  
> Created_at: 2020-04-28 18:11:45 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r416821684  

Would it make sense to replace `array` with your own clearly named type? It's a 2d vector correct? If that's the case then why not call it like that (e.g. `vec2d<>`) and have `x` and `y` members or use `boost::model::point_xy<>`?  
  
EDIT: my general comment about merging it as it is but not including by default here stands as well. However we could still consider using a clear name indicating what's happening instead of `std::array`.

> Username: tinko92  
> Created_at: 2020-04-28 21:35:48 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r416938280  

Thanks for the suggestion with regard to the interface, it is true that the values taken there correspond to points, so having points as parameters would be sensible. The matter of the remaining std::arrays would no longer be a matter of the interface of the function.  
  
I care a lot about the performance of those functions, though. Judging from the source code, I guess that point_xy and boost::array are zero-overhead like std::array but I'll verify that in the next couple of days via benchmarks/looking at the assembly and then I'll commit the necessary changes.

> Username: tinko92  
> Created_at: 2020-05-01 20:20:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r418720689  

@awulkiew I replaced the arrays for p1, p2, p3 with an own type (named vec2d). All other arrays in the function are expansions of single values and not 2d or nd vectors, so I did not touch those. Out of your two suggestions (own type and point_xy), I opted for my own type because I noticed that point_xy is not included almost nowhere (except in geometry/strategies/geographic/distance.hpp ), so I didn't want to create a header that depends on it.  
  
I'm fine with your general idea about not including side_robust anywhere by default before 1.75, So I did not change anything with regard to std::array and std::fma.

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp

```diff
 340 |+ 
 341 |+     // see p.39, mind the different definition of epsilon for error bound
 342 |+     RealNumber const A_relative_bound =
```

> Username: barendgehrels  
> Created_at: 2020-04-27 09:52:47 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r415672821  

We usually don't use capitals in variable names (unless there are good reasons to do so - and maybe you have them, if they are literally on the pages you refer to)

> Username: tinko92  
> Created_at: 2020-04-27 20:56:26 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r416142525  

That is indeed the reason. All capitals in variable names in this file where chosen to mimic the notation of Shewchuk's paper as closely as possible.


---

## Comment 2

> Username: tinko92  
> Created_at: 2020-04-27 10:07:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#issuecomment-619878190  

Thank you for the review, I will try to address all those issues by tomorrow evening.

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-28 18:01:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/701#pullrequestreview-402082602  

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp

```diff
 140 |-     RealNumber err3 = err2 - (a_expansion[0] * b_expansion[1]);
 141 |-     return (a_expansion[1] * b_expansion[1]) - err3;
 100 |+     return std::fma(a, b, -x);
```

> Username: awulkiew  
> Created_at: 2020-04-28 18:01:20 UTC  
> Updated_at: 2020-05-01 20:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#discussion_r416815316  

This also requires C++11 so either we have to wait til 1.75 or enable it conditionally based on the compiler. EDIT: or merge it as it is and simply not include this file by default.


---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-04-28 18:27:46 UTC  
> Updated_at: 2020-04-28 18:28:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/701#issuecomment-620779231  

Regarding the C++11 requirement. It would still be fine to merge it not only into the extensions but also in the official part of Geometry and even prior to 1.75 as long as this file is not included by any other file in the library that the user may be including right now. We already have C++11 code in one of the Karney geodesic formulas released. It is simply not included by any other file.

---
