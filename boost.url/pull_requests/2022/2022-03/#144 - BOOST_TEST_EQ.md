# #144 BOOST_TEST_EQ [Merged]

> Username: alandefreitas  
> Created at: 2022-03-16 19:22:18 UTC  
> Updated at: 2022-03-21 16:56:58 UTC  
> Merged at: 2022-03-18 18:03:53 UTC  
> Closed at: 2022-03-18 18:03:53 UTC  
> Url: https://github.com/boostorg/url/pull/144  

fix #69

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-03-16 20:01:06 UTC  
> Updated_at: 2022-03-16 20:01:18 UTC  
> Url: https://github.com/boostorg/url/pull/144#issuecomment-1069561581  

Error reporting has improved a lot. From  
  
```cpp  
url u1 = parse_uri("HtTp://cPpAlLiAnCe.oRG/").value();  
url u2 = parse_uri("http://cppalliance.org/aa/bb/cc/./../../gg").value();  
BOOST_TEST(u1 == u2);  
```  
  
```console  
#14248 url.cpp(1854) failed: u1 == u2  
```  
  
to   
  
```cpp  
BOOST_TEST_EQ(u1, u2);  
```  
  
```console  
#14249 url.cpp(1855) failed: 'u1 == u2' ('HtTp://cPpAlLiAnCe.oRG/' == 'http://cppalliance.org/aa/bb/cc/./../../gg')  
```  
  
, which is _very_ useful in "`check` functions.  
  
Because we cannot stream directly to the buffer, like lightweight_test does, some implementation details might still need some work.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-03-17 02:05:38 UTC  
> Updated_at: 2022-03-18 17:49:41 UTC  
> Url: https://github.com/boostorg/url/pull/144#issuecomment-1069844581  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/144?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#144](https://codecov.io/gh/CPPAlliance/url/pull/144?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3c53f47) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/5285e4fe46106dcc1db3f4fb35284ccf11141665?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (5285e4f) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 3c53f47 differs from pull request most recent head 999dbe1. Consider uploading reports for the commit 999dbe1 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/144/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #144   +/-   ##  
========================================  
  Coverage    96.71%   96.71%             
========================================  
  Files          110      110             
  Lines         6153     6153             
========================================  
  Hits          5951     5951             
  Misses         202      202             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/144?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/144?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [5285e4f...999dbe1](https://codecov.io/gh/CPPAlliance/url/pull/144?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-17 16:23:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/144#pullrequestreview-913325701  

📁 test/unit/authority_view.cpp

```diff
  32 |             BOOST_TEST(a.empty());
  33 |-             BOOST_TEST(a.size() == 0);
  33 |+             BOOST_TEST_EQ(a.size(), std::size_t(0));
```

> Username: vinniefalco  
> Created_at: 2022-03-17 16:23:25 UTC  
> Updated_at: 2022-03-17 16:23:26 UTC  
> Url: https://github.com/boostorg/url/pull/144#discussion_r829246310  

why not just `0` ?

> Username: alandefreitas  
> Created_at: 2022-03-17 18:34:08 UTC  
> Updated_at: 2022-03-17 18:34:09 UTC  
> Url: https://github.com/boostorg/url/pull/144#discussion_r829376580  

`0` emits lots of warnings because they are different types. We could implement something similar to [`std::cmp_equal`](https://en.cppreference.com/w/cpp/utility/intcmp) for the test operators, but the best solution is to directly use the proper types with `0u`, as all other boost libraries do.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-18 16:01:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/144#pullrequestreview-914559963  

📁 extra/include/test_suite.hpp

```diff
 221 |+ template <class T>
 222 |+ char const *
 223 |+ predicate_name( T const& )
```

> Username: vinniefalco  
> Created_at: 2022-03-18 16:01:42 UTC  
> Updated_at: 2022-03-18 16:01:43 UTC  
> Url: https://github.com/boostorg/url/pull/144#discussion_r830140696  

should this be `= delete`?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-18 16:02:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/144#pullrequestreview-914561702  

📁 extra/include/test_suite.hpp

```diff
 331 |+ 
 332 |+ template<typename... Ts>
 333 |+ using void_t = typename make_void<Ts...>::type;
```

> Username: vinniefalco  
> Created_at: 2022-03-18 16:02:57 UTC  
> Url: https://github.com/boostorg/url/pull/144#discussion_r830141964  

I think there's a Boost include (from another lib) that gives you make_void, maybe look in Boost.TypeTraits?

> Username: alandefreitas  
> Created_at: 2022-03-18 17:29:00 UTC  
> Updated_at: 2022-03-18 17:29:01 UTC  
> Url: https://github.com/boostorg/url/pull/144#discussion_r830211423  

Oh... I thought `test_suite.hpp` was purposefully not including other boost libs.

> Username: alandefreitas  
> Created_at: 2022-03-18 17:49:38 UTC  
> Updated_at: 2022-03-18 17:49:39 UTC  
> Url: https://github.com/boostorg/url/pull/144#discussion_r830227325  

Done


---
