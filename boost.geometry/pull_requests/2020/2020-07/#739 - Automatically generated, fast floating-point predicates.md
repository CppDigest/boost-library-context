# #739  Automatically generated, fast floating-point predicates [Open]

> Username: tinko92  
> Created at: 2020-07-28 11:38:48 UTC  
> Updated at: 2021-03-04 22:39:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/739  

This pull request contains the first part of my GSoC 2020 project. This code is still work in progress and there might be changes to the interface before the end of GSoC.  
  
The project aims to implement a number of facilities that allow easy creation of fast and robust geometric floating-point predicates, such as, for example, orientation tests (Boost.Geometry currently has a 2d orientation test in the side-strategy). As of writing this, the pull request contains templates to   
  
- automatically derive error bounds for the approximate evaluation of such expressions  
- build static, almost static and semi-static filters from these expressions  
  
I intend to add  
- generation of functions for the exact evaluation of arbitrary arithmetic floating-point expressions containing +, - and * using expansion arithmetic  
- improvements for the error bound derivation  
- an alternative method for error bound derivation  
- some additional filters  
- automatic detection of reusable interim results between filter stages  
  
The usage of the various templates for the creation of geometric predicates is illustrated in the tests and the example.  
  
This pull request depends on Boost.MP11, which depends on C++11 but has no additional dependencies. The code itself depends on C++14 because of certain constexpr features.

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2020-07-31 19:42:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/739#pullrequestreview-459059840  

Thanks Tinko, this is very interesting. I have minor comments.

📁 extensions/example/generic_robust_predicates/static_side_2d.cpp

```diff
  57 |+                                   bg::get<0>(p),
  58 |+                                   bg::get<1>(p));
  59 |+         if(sign != bg::detail::generic_robust_predicates::sign_uncertain)
```

> Username: vissarion  
> Created_at: 2020-07-31 10:03:31 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463522295  

a space missing after `if`

---

📁 extensions/example/generic_robust_predicates/static_side_2d.cpp

```diff
  69 |+ };
  70 |+ 
  71 |+ int main(int argc, char** argv)
```

> Username: vissarion  
> Created_at: 2020-07-31 10:04:14 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463522627  

`argc, argv` are unused, please compile with `-Wall` since there are a few more unused variables in this PR.


📁 include/boost/geometry/extensions/generic_robust_predicates/strategies/cartesian/detail/almost_static_filter.hpp

```diff
  29 |+ {
  30 |+     template <typename ExtremaArray, typename ...Reals>
  31 |+     static Filter apply(const ExtremaArray& extrema, const Reals&... args)
```

> Username: vissarion  
> Created_at: 2020-07-31 10:34:19 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463535620  

`const ExtremaArray&` should be`ExtremaArray const&`, similar for `const ExtremaArray&`

---

📁 include/boost/geometry/extensions/generic_robust_predicates/strategies/cartesian/detail/almost_static_filter.hpp

```diff
  40 |+ {
  41 |+     template <typename ExtremaArray, typename ...Reals>
  42 |+     static Filter apply(const ExtremaArray& extrema, const Reals&... args)
```

> Username: vissarion  
> Created_at: 2020-07-31 10:34:53 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463535870  

same here

---

📁 include/boost/geometry/extensions/generic_robust_predicates/strategies/cartesian/detail/almost_static_filter.hpp

```diff
  93 |+ 
  94 |+     template <typename ...Reals>
  95 |+     inline bool update_extrema_check(const Reals&... args)
```

> Username: vissarion  
> Created_at: 2020-07-31 10:36:15 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463536430  

same here

---

📁 include/boost/geometry/extensions/generic_robust_predicates/strategies/cartesian/detail/almost_static_filter.hpp

```diff
  79 |+ 
  80 |+     template <typename ...Reals>
  81 |+     inline void update_extrema(const Reals&... args)
```

> Username: vissarion  
> Created_at: 2020-07-31 10:36:21 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463536478  

same hare


📁 include/boost/geometry/extensions/generic_robust_predicates/strategies/cartesian/detail/approximate.hpp

```diff
 111 |+     typename InputArr
 112 |+ >
 113 |+ inline Real get_approx(Arr& interim_results, const InputArr& input)
```

> Username: vissarion  
> Created_at: 2020-07-31 10:38:31 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463537367  

usually the output parameter(s) goes after the input parameter(s) in the library  
also  `const InputArr&` should be  `InputArr const&`


📁 include/boost/geometry/extensions/generic_robust_predicates/strategies/cartesian/detail/expressions.hpp

```diff
  33 |+ 
  34 |+ using orient2d = det2x2
  35 |+         <
```

> Username: vissarion  
> Created_at: 2020-07-31 19:40:22 UTC  
> Updated_at: 2021-03-04 22:15:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#discussion_r463797405  

why 8 spaces here vs 4 above?


---

## Comment 2

> Username: tinko92  
> Created_at: 2021-03-04 22:39:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/739#issuecomment-790995902  

I have made the following changes to the PR:  
  
- The template-based error bound calculation has been replaced by a constexpr based implementation. This improves compile-time, template instantiation depth and readability.  
- I have tried to address all feedback (spaces after if, unused parameters, position of "const" in types"  
- Staged predicates have been added.  
  
The PR now includes a new test-case in https://github.com/BoostGSoC20/geometry/blob/feature/floating_point_filters/extensions/test/generic_robust_predicates/staged.cpp . It illustrates the intended use of the code in this PR.   
  
For a given geometric predicate (in the example the side-predicate), an expression type needs to be constructed. Using the expression type and a floating-point calculation type (like double), filters can be constructed from the stage_* files. These filters can be combined into a staged_predicate for usage, for example, in a robust side strategy or any other strategy that can be computed as the sign of a polynomial of floating-point coordinates.  
  
The remaining headers are not intended to be used directly and can be considered implementation details.

---
