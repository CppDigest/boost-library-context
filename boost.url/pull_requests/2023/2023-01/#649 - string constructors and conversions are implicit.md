# #649 string constructors and conversions are implicit [Merged]

> Username: alandefreitas  
> Created at: 2023-01-03 20:27:10 UTC  
> Updated at: 2023-07-24 14:36:49 UTC  
> Merged at: 2023-01-19 01:23:31 UTC  
> Closed at: 2023-01-19 01:23:31 UTC  
> Url: https://github.com/boostorg/url/pull/649  

fix #648, #650

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-01-03 20:42:01 UTC  
> Url: https://github.com/boostorg/url/pull/649#issuecomment-1370205258  

GCOVR code coverage report [https://649.url.prtest.cppalliance.org/gcovr/index.html](https://649.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://649.url.prtest.cppalliance.org/genhtml/index.html](https://649.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-01-03 21:49:22 UTC  
> Url: https://github.com/boostorg/url/pull/649#issuecomment-1370258833  

GCOVR code coverage report [https://649.url.prtest.cppalliance.org/gcovr/index.html](https://649.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://649.url.prtest.cppalliance.org/genhtml/index.html](https://649.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-01-04 00:02:31 UTC  
> Updated_at: 2023-01-05 16:53:56 UTC  
> Url: https://github.com/boostorg/url/pull/649#issuecomment-1370344300  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#649](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (646bf7b) into [develop](https://codecov.io/gh/boostorg/url/commit/af8aa0388769fb2d9c44a9ef8919acd215739df3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (af8aa03) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/649/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #649      +/-   ##  
===========================================  
- Coverage    96.88%   96.87%   -0.02%       
===========================================  
  Files          147      147                
  Lines         7924     7929       +5       
===========================================  
+ Hits          7677     7681       +4       
- Misses         247      248       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `91.66% <0.00%> (-4.17%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [af8aa03...646bf7b](https://codecov.io/gh/boostorg/url/pull/649?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2023-01-05 15:28:51 UTC  
> Url: https://github.com/boostorg/url/pull/649#issuecomment-1372366054  

@klemens-morgenstern is this ok?

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-05 16:10:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/649#pullrequestreview-1237659919  

📁 include/boost/url/url_view.hpp

```diff
 211 |+     >
 212 |+     url_view(
 213 |+         String const& s)
```

> Username: vinniefalco  
> Created_at: 2023-01-05 16:10:09 UTC  
> Url: https://github.com/boostorg/url/pull/649#discussion_r1062645520  

now you have two overloads which are the same no? string_view is convertible to string_view. Does this compile?  
```  
string_view s = "";  
url_view u( s );  
```  
  
Isn't this ambiguous?

> Username: alandefreitas  
> Created_at: 2023-01-05 16:24:06 UTC  
> Updated_at: 2023-01-05 16:24:07 UTC  
> Url: https://github.com/boostorg/url/pull/649#discussion_r1062658881  

It works fine. It's exactly the same pattern as pct_string_view:  
  
- the compiler picks up the `string_view` overload because it's not templated  
- the compiler picks up the `String` overload when it's convertible to `string_view` but not `string_view`  
  
If all things were directly convertible to `string_view`, we would just need the first overload. But we have two implicit conversion steps sometimes.  
  
I included your test to ensure that's OK. The test is somewhat redundant because most other tests already construct these `url_view`s from `string_view`s, but that's not explicit..


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-05 16:24:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/649#pullrequestreview-1237680482  

📁 test/unit/url_view.cpp

```diff
  95 |+         {
  96 |+             string_view s = "";
  97 |+             BOOST_TEST_NO_THROW(url_view( s ));
```

> Username: vinniefalco  
> Created_at: 2023-01-05 16:24:04 UTC  
> Url: https://github.com/boostorg/url/pull/649#discussion_r1062658832  

Because the template overload has a lower priority than the string_view overload?

> Username: alandefreitas  
> Created_at: 2023-01-05 16:24:23 UTC  
> Url: https://github.com/boostorg/url/pull/649#discussion_r1062659191  

Yes :)


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-01-05 16:34:23 UTC  
> Url: https://github.com/boostorg/url/pull/649#issuecomment-1372449301  

GCOVR code coverage report [https://649.url.prtest.cppalliance.org/gcovr/index.html](https://649.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://649.url.prtest.cppalliance.org/genhtml/index.html](https://649.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2023-01-06 00:24:17 UTC  
> Url: https://github.com/boostorg/url/pull/649#issuecomment-1372969296  

I would have thought a ctor from stringview is eny, but I will trust your judgement. looks good.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-01-12 22:54:19 UTC  
> Url: https://github.com/boostorg/url/pull/649#issuecomment-1381082661  

GCOVR code coverage report [https://649.url.prtest.cppalliance.org/gcovr/index.html](https://649.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://649.url.prtest.cppalliance.org/genhtml/index.html](https://649.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://649.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
