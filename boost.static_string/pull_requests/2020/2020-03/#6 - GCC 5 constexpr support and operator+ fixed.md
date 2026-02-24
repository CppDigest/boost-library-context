# #6 GCC 5 constexpr support and operator+ fixed  [Merged]

> Username: sdkrystian  
> Created at: 2020-03-10 03:01:27 UTC  
> Updated at: 2020-03-12 02:47:16 UTC  
> Merged at: 2020-03-12 02:47:16 UTC  
> Closed at: 2020-03-12 02:47:16 UTC  
> Url: https://github.com/boostorg/static_string/pull/6  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-10 03:19:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/6#pullrequestreview-371658708  

📁 include/boost/static_string/static_string.hpp

```diff
 736 |+ // constructors.
 737 |+ template<typename Exception>
 738 |+ struct exception
```

> Username: vinniefalco  
> Created_at: 2020-03-10 03:19:18 UTC  
> Updated_at: 2020-03-12 02:36:17 UTC  
> Url: https://github.com/boostorg/static_string/pull/6#discussion_r390074667  

I would `#ifdef` out anything in detail namespaces (including the namespace declaration) when building the docs. There's no reason for doxygen to see those symbols, and sometimes doxygen has a bug which causes the doc build to fail.

> Username: sdkrystian  
> Created_at: 2020-03-10 03:24:37 UTC  
> Updated_at: 2020-03-12 02:36:17 UTC  
> Url: https://github.com/boostorg/static_string/pull/6#discussion_r390075917  

This is already hidden


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-10 03:20:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/6#pullrequestreview-371659091  

📁 include/boost/static_string/static_string.hpp

```diff
 746 |+       const char* message)
 747 |+     {
 748 |+       BOOST_STATIC_STRING_THROW_IF(
```

> Username: vinniefalco  
> Created_at: 2020-03-10 03:20:53 UTC  
> Updated_at: 2020-03-12 02:36:17 UTC  
> Url: https://github.com/boostorg/static_string/pull/6#discussion_r390075053  

This looks like a lot of unnecessary complexity why are we doing this bool check and the THROW_IF and all of that?  
  
The `bool check` is going to make coverage reports less reliable because you are hiding the condition in the macro.

> Username: sdkrystian  
> Created_at: 2020-03-10 03:25:22 UTC  
> Updated_at: 2020-03-12 02:36:17 UTC  
> Url: https://github.com/boostorg/static_string/pull/6#discussion_r390076062  

I suppose we can get rid of `THROW_IF` now that we only use it in a single place

> Username: vinniefalco  
> Created_at: 2020-03-10 03:27:02 UTC  
> Updated_at: 2020-03-12 02:36:17 UTC  
> Url: https://github.com/boostorg/static_string/pull/6#discussion_r390076438  

For example look here:  
https://codecov.io/gh/sdkrystian/static_string/src/01996796a0789c1a4c1ebe116853c8f250a6c1bf/include/boost/static_string/static_string.hpp#L1609  
  
How do we know that we are getting coverage for both cases, where `pos >= size()` and `pos < size()`? The report doesn't show it, because you have hidden it in a macro behind a member function behind a class template. The function `at` should be written thusly:  
```  
#ifndef BOOST_STATIC_STRING_DOCS  
namespace detail {  
struct out_of_range_error : std::out_of_range {  
  static void raise(char const* s) {  
    BOOST_THROW_EXCEPTION( detail::out_of_range_error(s) );  
  }  
} // detail  
#endif  
  
BOOST_STATIC_STRING_CPP14_CONSTEXPR  
reference  
at(size_type pos)  
{  
  if( pos >= size() )  
    detail::out_of_range_error::raise();  
  return data()[pos];  
}  
```  
  
By making the conditional its own line in the function, we can tell whether both branches of the `if` statement are exercised.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-03-10 03:27:40 UTC  
> Updated_at: 2020-03-12 02:36:20 UTC  
> Url: https://github.com/boostorg/static_string/pull/6#issuecomment-596883168  

# [Codecov](https://codecov.io/gh/boostorg/static_string/pull/6?src=pr&el=h1) Report  
> Merging [#6](https://codecov.io/gh/boostorg/static_string/pull/6?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/static_string/commit/2584ab24001666b425c51022270481190bbfdd94?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/static_string/pull/6/graphs/tree.svg?width=650&token=LSsgELMWac&height=150&src=pr)](https://codecov.io/gh/boostorg/static_string/pull/6?src=pr&el=tree)  
  
```diff  
@@          Coverage Diff           @@  
##           develop     #6   +/-   ##  
======================================  
  Coverage      100%   100%             
======================================  
  Files            1      1             
  Lines          824    850   +26       
======================================  
+ Hits           824    850   +26  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/static_string/pull/6?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/static\_string/static\_string.hpp](https://codecov.io/gh/boostorg/static_string/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9zdGF0aWNfc3RyaW5nL3N0YXRpY19zdHJpbmcuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/static_string/pull/6?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/static_string/pull/6?src=pr&el=footer). Last update [2584ab2...8bef806](https://codecov.io/gh/boostorg/static_string/pull/6?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
