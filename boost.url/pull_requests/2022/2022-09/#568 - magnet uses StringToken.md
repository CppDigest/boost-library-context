# #568 magnet uses StringToken [Closed]

> Username: alandefreitas  
> Created at: 2022-09-26 19:30:22 UTC  
> Updated at: 2022-12-22 16:23:54 UTC  
> Closed at: 2022-10-06 02:52:59 UTC  
> Url: https://github.com/boostorg/url/pull/568  

fix #535

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-09-26 19:38:31 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258527588  

GCOVR code coverage report [https://568.url.prtest.cppalliance.org/gcovr/index.html](https://568.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://568.url.prtest.cppalliance.org/genhtml/index.html](https://568.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-09-26 19:42:34 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258531623  

An automated preview of the documentation is available at [https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2022-09-26 20:39:21 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258607689  

I believe the new rules for query/params_ref broke something with magnet.  For instance,  
  
```cpp  
    auto xt = m.exact_topics();  
    for (auto h : xt)  
        std::cout << "topic: " << h << "\n";  
```  
  
is now creating a url_view to the wrong address, even before removing MutableString, but I still not being able to figure out why.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-09-26 20:47:20 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258615201  

An automated preview of the documentation is available at [https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-09-26 20:48:27 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258616664  

GCOVR code coverage report [https://568.url.prtest.cppalliance.org/gcovr/index.html](https://568.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://568.url.prtest.cppalliance.org/genhtml/index.html](https://568.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-09-26 20:53:15 UTC  
> Updated_at: 2022-10-06 02:50:58 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258620575  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/568?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#568](https://codecov.io/gh/boostorg/url/pull/568?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fe62aeb) into [develop](https://codecov.io/gh/boostorg/url/commit/1fab18b28d4558d9035fdf49f4176ec3e45c3b98?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1fab18b) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head fe62aeb differs from pull request most recent head bd4ce2b. Consider uploading reports for the commit bd4ce2b to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/568/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/568?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #568   +/-   ##  
========================================  
  Coverage    96.81%   96.81%             
========================================  
  Files          139      139             
  Lines         6688     6691    +3       
========================================  
+ Hits          6475     6478    +3       
  Misses         213      213             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/568?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url.hpp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/static\_url.hpp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `80.00% <0.00%> (ø)` | |  
| [include/boost/url/ipv4\_address.hpp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2NF9hZGRyZXNzLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/ipv6\_address.hpp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2Nl9hZGRyZXNzLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.02% <0.00%> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `98.59% <0.00%> (ø)` | |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/boostorg/url/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `96.15% <0.00%> (+0.50%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/568?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/568?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1fab18b...bd4ce2b](https://codecov.io/gh/boostorg/url/pull/568?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-09-26 21:17:33 UTC  
> Updated_at: 2022-09-26 21:17:44 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258643020  

> I believe the new rules for query/params_ref broke something with magnet.   
  
That suspicion was correct. The old interface that returned string views cannot work anymore because params return references to temporary strings that don't exist after the view is dereferenced.  
  
Everything should be good now.

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-09-26 21:27:23 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258652711  

An automated preview of the documentation is available at [https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-09-26 21:29:58 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1258655121  

GCOVR code coverage report [https://568.url.prtest.cppalliance.org/gcovr/index.html](https://568.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://568.url.prtest.cppalliance.org/genhtml/index.html](https://568.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-10-05 15:31:44 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1268600681  

An automated preview of the documentation is available at [https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://568.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-10-05 15:32:59 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1268601856  

GCOVR code coverage report [https://568.url.prtest.cppalliance.org/gcovr/index.html](https://568.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://568.url.prtest.cppalliance.org/genhtml/index.html](https://568.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2022-10-05 19:56:58 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1268900989  

good to go if you think its ready

---

## Comment 13

> Username: alandefreitas  
> Created_at: 2022-10-06 02:52:59 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1269237552  

Merged

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-10-06 02:59:19 UTC  
> Url: https://github.com/boostorg/url/pull/568#issuecomment-1269241165  

GCOVR code coverage report [https://568.url.prtest.cppalliance.org/gcovr/index.html](https://568.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://568.url.prtest.cppalliance.org/genhtml/index.html](https://568.url.prtest.cppalliance.org/genhtml/index.html)

---
