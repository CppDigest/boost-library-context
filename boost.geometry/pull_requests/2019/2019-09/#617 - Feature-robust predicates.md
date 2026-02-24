# #617 Feature/robust predicates [Merged]

> Username: tinko92  
> Created at: 2019-09-04 11:00:45 UTC  
> Updated at: 2019-11-12 17:16:57 UTC  
> Merged at: 2019-10-19 15:09:59 UTC  
> Closed at: 2019-10-19 15:09:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/617  

# Introduction  
This is the first (and most simple and self-contained) of three branches that I created during the GSoC 2019 that has now concluded. It provides two strategies for 2d cartesian points:  
* a side-strategy that is basically a robust, adaptive alternative to side_by_triangle (more details below). Given p1, p2, p3 it calculates the signed area (times two) of the triangle p1, p2, p3 and (based on the sign) answers the question: Does p3 lie on the left or right side or exactly on the segment p1, p2.  
* an in-circle-strategy for 4 points p1, p2, p3, p, that answers the question: Does p lie in the circumcircle of the triangle formed by p1, p2, p3?  
  
The strategy classes come with documentation and tests, currently in the extension-directories.  
  
# Rationale  
The new side-strategy can be used where side_by_triangle is used and may help in situations where computations break down because of incorrect results in side_by_triangle.  
The in_circle-strategy will be required by algorithms for the construction of Delaunay triangulations such as the one I will propose in a later pull request. It may also be of use in constructing an adaptive, robust side-strategy for circular segments, which were recently proposed by @meastp in the Gitter room and generally useful on its own for users.  
  
# Implementation details  
Both strategies are based on evaluating certain determinants (like the existing side_by_triangle), They are robust in the sense that they return correct results even on inputs where direct float arithmetics would fail due to precision issues, such as side of POINT(1, 1) with respect to the segment of POINT( 1e-20, 0) and POINT(1e20, 1e20). They are adaptive in the sense that for inputs where direct float arithmetic can provably produce correct results (like in all non-degenerate inputs), the computation terminates early to save time. This is done using analytically computed error bounds. The algorithm is intended for floating-point parameters but returns correct results for integers that can be translated to the calculation_type without loss of precision. It is not robust with respect to inputs that cause an overflow in the computation of interim results.  
  
All of the math behind this is explained in the papers linked here https://www.cs.cmu.edu/~quake/robust.html. The details are implemented almost directly as explained in the paper. All of the implementation details (the mathematics) are in the file   
include/boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp  
  
The robustness-parameter is my own addition and can be used to limit the maximum precision.  If 0 is provided, the predicates are comparatively fast.

---

## Review 1 [Changes requested]

> Username: vissarion  
> Created_at: 2019-10-09 09:51:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/617#pullrequestreview-299280121  

Thanks for this PR! I am ok with the math, the computations seem ok too. However, the strategy needs some cleaning/revision.

📁 extensions/test/triangulation/in_circle_robust.cpp

```diff
   3 |+ 
   4 |+ // Copyright (c) 2019 Tinko Bartels, Berlin, Germany.
   5 |+ 
```

> Username: vissarion  
> Created_at: 2019-10-09 09:27:44 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r332914062  

You could acknowledge GSoC here if you like. See for example https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/karney_direct.hpp#L5


📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp

```diff
  24 |+ \ingroup strategies
  25 |+ \tparam CalculationType \tparam_calculation (numeric_limits<ct>::epsilon() and numeric_limits<ct>::digits must be supported for calculation type ct)
  26 |+ \tparam robustness Number that determines maximum precision. Values from 0 to 2 may make the calculation terminate faster for inputs that may require higher precision to ensure correctness.
```

> Username: vissarion  
> Created_at: 2019-10-09 09:31:57 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r332915980  

this is not very clear. Do you want to say values from 0 to 3, where 0 is the fastest and less accurate and 3 the slowest and most accurate ?

> Username: tinko92  
> Created_at: 2019-10-10 23:04:21 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r333770824  

I understand, I'll reconsider the wording there. It is a little hard to say it in one sentence.  
By default (robustness = 3), the strategy will compute up to four successively more precise approximations of the true side_value (with the fourth being guaranteed to be correct) and check for each approximation whether its sign can already be guaranteed using relative error bounds. If that is the case, the remaining approximations (if any) will not be computed and the current approximation will be returned.   
  
If the robustness parameter is set to 0, it will return the first approximation without any test against the error bounds, if it is set to 1, it will compute the first approximation, test it against the error bound and either return if the precision is sufficient or compute the second approximation. With robustness = 2, up to three approximations will be computed.

> Username: vissarion  
> Created_at: 2019-10-14 08:35:02 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r334373116  

For me, the technical details are not needed here. Hence, saying something like 0 is the fastest and less accurate and 3 the slowest and most accurate, is fine.

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp

```diff
  38 |+     template
  39 |+     <
  40 |+         typename CoordinateType,
```

> Username: vissarion  
> Created_at: 2019-10-09 09:35:17 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r332917674  

unused template parameter

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp

```diff
  61 |+         typename P
  62 |+     >
  63 |+     static inline int apply(P1 const& p1, P2 const& p2, P const& p)
```

> Username: vissarion  
> Created_at: 2019-10-09 09:40:50 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r332920009  

maybe this is too generic. What is the case where `p1` and `p2` are of different types?

> Username: tinko92  
> Created_at: 2019-10-10 23:07:36 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r333771588  

I cannot imagine such a case, however, my choice to be that generic was motivated by side_by_triangle having the same interface and I wanted to be as compatible with it as possible.

> Username: barendgehrels  
> Created_at: 2019-10-12 10:38:57 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r334231834  

Yes, the same interface is OK.

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp

```diff
  70 |+             <
  71 |+                 boost::is_void<CalculationType>::type::value,
  72 |+                 typename select_most_precise
```

> Username: vissarion  
> Created_at: 2019-10-09 09:45:37 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r332922147  

there is a `select_most_precise` with 3 arguments

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp

```diff
  67 |+         typedef typename coordinate_type<P>::type coordinate_type3;
  68 |+ 
  69 |+         typedef typename boost::mpl::if_c
```

> Username: vissarion  
> Created_at: 2019-10-09 09:47:25 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r332922966  

this is implemented in utilities as `select_calculation_type_alt`

---

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp

```diff
  88 |+ 
  89 |+         promoted_type sv =
  90 |+             side_value<coordinate_type, promoted_type>(p1, p2, p);
```

> Username: vissarion  
> Created_at: 2019-10-09 09:47:58 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r332923183  

`coordinate_type` here is unused


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2019-10-12 10:39:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/617#pullrequestreview-300999385  

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp

```diff
  34 |+ <
  35 |+     typename CalculationType = void,
  36 |+     int robustness = 3
```

> Username: barendgehrels  
> Created_at: 2019-10-12 10:37:27 UTC  
> Updated_at: 2019-10-17 21:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#discussion_r334231786  

Tinko, thanks a lot for your contributions!  
Can you spell the robustness template parameter as `Robustness` and maybe make it an std::size_t


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2019-10-14 08:39:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/617#pullrequestreview-301164177  

Tinko thanks for the revision, I am ok with merging now.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2019-10-16 18:10:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#issuecomment-542825334  

Thanks for this PR! I'm ok with merging after the issues above are addressed.  
  
I'd like to be explicit regarding one thing though. This PR depends on C++11. After merging it the library will partially depend on newer standard. I'm ok with that since we plan to move to C++11 anyway, but I'd like to get explicit confirmation from you @barendgehrels @vissarion.

---

## Comment 5

> Username: vissarion  
> Created_at: 2019-10-17 05:58:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#issuecomment-543016753  

@awulkiew I am OK to merge this since it is currently in extensions. Also, since in the discussion here: https://github.com/boostorg/geometry/issues/590 we haven't reached to a conclusion on a plan to move to newer standards, adding new features that depend on newer standards in extensions could be a start.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2019-10-19 15:09:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/617#issuecomment-544157534  

Thanks Tinko!

---
