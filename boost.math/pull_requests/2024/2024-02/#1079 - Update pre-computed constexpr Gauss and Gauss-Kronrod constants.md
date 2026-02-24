# #1079 Update pre-computed constexpr Gauss and Gauss-Kronrod constants [Merged]

> Username: jzmaddock  
> Created at: 2024-02-01 18:44:35 UTC  
> Updated at: 2024-02-09 18:18:09 UTC  
> Merged at: 2024-02-09 12:28:23 UTC  
> Closed at: 2024-02-09 12:28:23 UTC  
> Url: https://github.com/boostorg/math/pull/1079  

to always store literal types.  
Fixes: https://github.com/boostorg/math/issues/1077.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-01 20:58:56 UTC  
> Updated_at: 2024-02-01 21:44:57 UTC  
> Url: https://github.com/boostorg/math/pull/1079#issuecomment-1922232987  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`ca29a70`)](https://app.codecov.io/gh/boostorg/math/commit/ca29a700f1ff7d508004a8843b07457e505b8b94?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.21% compared to head [(`b2b839a`)](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 84.90%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1079/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1079      +/-   ##  
===========================================  
- Coverage    85.21%   84.90%   -0.31%       
===========================================  
  Files          879      880       +1       
  Lines        66880    67275     +395       
===========================================  
+ Hits         56989    57120     +131       
- Misses        9891    10155     +264       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/quadrature/gauss.hpp](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZ2F1c3MuaHBw) | `65.03% <100.00%> (-25.10%)` | :arrow_down: |  
| [include/boost/math/quadrature/gauss\_kronrod.hpp](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZ2F1c3Nfa3JvbnJvZC5ocHA=) | `49.31% <100.00%> (-45.90%)` | :arrow_down: |  
| [test/git\_issue\_1075.cpp](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTA3NS5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ca29a70...b2b839a](https://app.codecov.io/gh/boostorg/math/pull/1079?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: SteveBronder  
> Created_at: 2024-02-01 22:21:56 UTC  
> Updated_at: 2024-02-01 22:32:59 UTC  
> Url: https://github.com/boostorg/math/pull/1079#issuecomment-1922368400  

How would you feel about something like the below? I wrote a `is_constexpr_constructible()` function that is used to deduce which `gauss_detail` to use. If `Real`'s constant type is constructible from a long double at compile time (which I think is true for `float`, `double`, and `long double`)  then it uses the one that is constexpr. Otherwise it uses one that constructs the values at runtime.   
  
Looking at the version here I think something bad would happen across if we ran this once, cleaned up the arena, and tried running again. I'm always bad at the intricacies of static types in functions, but if they are declared once for the life of the program and we clean up the memory arena then I think the next time we run this the program would do UB accessing memory that has been cleaned up or reallocated.  
  
https://godbolt.org/z/j1br4GTno  
  
EDIT: updated link to compile with c++14

---

## Review 3 [Commented]

> Username: SteveBronder  
> Created_at: 2024-02-02 22:16:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1079#pullrequestreview-1860481437  

Just a few small comments. I'm not really following how this fixes https://github.com/boostorg/math/issues/1077

📁 include/boost/math/quadrature/gauss_kronrod.hpp

```diff
 163 |    {
 229 |-       static  std::array<T, 8> data = {
 164 |+       static  std::array<T, 8> data = { // LCOV_EXCL_START
```

> Username: SteveBronder  
> Created_at: 2024-02-02 22:07:57 UTC  
> Updated_at: 2024-02-02 22:16:33 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1476792063  

Is `LCOV_EXCL_START` documented somewhere?

> Username: jzmaddock  
> Created_at: 2024-02-03 18:27:22 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1477109930  

It's used by the lcov tool when collecting code coverage data (ie it's documented by the lcov tool).  For some reason GCC has trouble instrumenting arrays which are dynamically initialized, with only the final value being marked as covered, so we have to exclude those lines from coverage analysis.

---

📁 include/boost/math/quadrature/gauss_kronrod.hpp

```diff
  85 |+ #ifndef BOOST_HAS_FLOAT128
  86 | template <class T>
  87 | class gauss_kronrod_detail<T, 15, 0>
```

> Username: SteveBronder  
> Created_at: 2024-02-02 22:09:41 UTC  
> Updated_at: 2024-02-02 22:16:34 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1476793095  

Do you need to have the final template parameter be an integer or can you just use the `gauss_constant_category<T>::storage_type` as the final template parameter?

> Username: jzmaddock  
> Created_at: 2024-02-03 18:28:04 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1477110015  

Not quite, we now have 3 cases, two of which both use T as storage_type.


📁 include/boost/math/quadrature/gauss.hpp

```diff
  33 |+          (std::numeric_limits<T>::digits <= 113) && std::is_constructible<__float128, T>::value ? 0 :
  34 |+ #else
  35 |+          (std::numeric_limits<T>::digits <= std::numeric_limits<long double>::digits) && std::is_convertible<long double, T>::value ? 0 :
```

> Username: SteveBronder  
> Created_at: 2024-02-02 22:12:10 UTC  
> Updated_at: 2024-02-02 22:16:34 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1476794664  

Is there a reason to have the value now that you deduce the storage type here?

> Username: jzmaddock  
> Created_at: 2024-02-03 18:32:38 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1477110525  

As above.

---

📁 include/boost/math/quadrature/gauss.hpp

```diff
 110 |+          static_cast<storage_type>(4.05845151377397166906606412076961463e-01L),
 111 |+          static_cast<storage_type>(7.41531185599394439863864773280788407e-01L),
 112 |+          static_cast<storage_type>(9.49107912342758524526189684047851262e-01L),
```

> Username: SteveBronder  
> Created_at: 2024-02-02 22:13:54 UTC  
> Updated_at: 2024-02-02 22:16:34 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1476795612  

What is the reasoning behind putting have the function return a const& to a constexpr type? i.e. why can't the signature just be `constexpr`?

> Username: jzmaddock  
> Created_at: 2024-02-03 18:32:14 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1477110421  

I don't think functions returning arrays are allowed?  But also just because the function is marked as constexpr, does not mean that it will be evaluated as such, only that it can be.  We could probably mark the existing signatures constexpr, but I'm not sure it actually makes any practical difference - either it's inlined and optimised away or not, either which way, we can never actually call the function in a constexpr context since some overloads (for the multiprecision cases) cannot be constexpr.

> Username: SteveBronder  
> Created_at: 2024-02-06 16:35:06 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1480189473  

> I don't think functions returning arrays are allowed?  
  
In c++14? That should be fine  
  
> But also just because the function is marked as constexpr, does not mean that it will be evaluated as such, only that it can be.  
  
If a function can be constexpr idt the compiler gets a choice on whether to make it constexpr or not. At least reading over the constexpr function spec from cppreference I don't see anything that says that constexpr function invocations are compiler defined.  
  
> either it's inlined and optimised away or not, either which way,   
  
Making those constexpr removes the static here which imo does matter for user defined types  
  
> we can never actually call the function in a constexpr context since some overloads (for the multiprecision cases) cannot be constexpr.  
  
See the constexpr function docs section [here](https://en.cppreference.com/w/cpp/language/constexpr). For any of the overloads that can be constexpr this will be.

> Username: jzmaddock  
> Created_at: 2024-02-09 12:27:51 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1484256332  

>Making those constexpr removes the static here which imo does matter for user defined types  
  
Right, we do actually want the functions static in the general case (since there is no need for a `this` pointer).  In any case, unless someone demonstrates that substantially better code can be produced with a different signature, I'll leave as is for now.

---

📁 include/boost/math/quadrature/gauss.hpp

```diff
  41 |+       typename std::conditional<(std::numeric_limits<T>::is_specialized == 0), T,
  42 |+          typename std::conditional<(std::numeric_limits<T>::radix == 2),
  43 |+             typename std::conditional< ((std::numeric_limits<T>::digits <= std::numeric_limits<float>::digits) && std::is_convertible<float, T>::value),
```

> Username: SteveBronder  
> Created_at: 2024-02-02 22:14:29 UTC  
> Updated_at: 2024-02-02 22:16:34 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1476795962  

The math library requires a c++14 compiler now so you should be able to use `std::conditional_t` now to remove the typename and ::type

> Username: jzmaddock  
> Created_at: 2024-02-03 18:32:49 UTC  
> Url: https://github.com/boostorg/math/pull/1079#discussion_r1477110541  

Done.


---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-02-04 09:35:22 UTC  
> Url: https://github.com/boostorg/math/pull/1079#issuecomment-1925658932  

I hope this should be good to go: @SteveBronder can you try this PR against your actual code?

---

## Comment 5

> Username: SteveBronder  
> Created_at: 2024-02-04 18:18:28 UTC  
> Url: https://github.com/boostorg/math/pull/1079#issuecomment-1925871196  

Thanks I'll give this a whirl on Monday!

---

## Comment 6

> Username: SteveBronder  
> Created_at: 2024-02-06 17:24:36 UTC  
> Url: https://github.com/boostorg/math/pull/1079#issuecomment-1930416898  

This works!

---

## Comment 7

> Username: NAThompson  
> Created_at: 2024-02-08 19:57:58 UTC  
> Url: https://github.com/boostorg/math/pull/1079#issuecomment-1934842234  

@jzmaddock : Looks like the build failure is unrelated-want to merge?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2024-02-09 12:28:13 UTC  
> Url: https://github.com/boostorg/math/pull/1079#issuecomment-1935842798  

> Looks like the build failure is unrelated-want to merge?  
  
Yup, merging...

---
