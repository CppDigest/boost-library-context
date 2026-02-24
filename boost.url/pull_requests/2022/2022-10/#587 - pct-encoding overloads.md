# #587 pct-encoding overloads [Merged]

> Username: vinniefalco  
> Created at: 2022-10-10 01:33:50 UTC  
> Updated at: 2022-10-11 20:52:06 UTC  
> Merged at: 2022-10-10 23:23:18 UTC  
> Closed at: 2022-10-10 23:23:18 UTC  
> Url: https://github.com/boostorg/url/pull/587  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-10 01:43:11 UTC  
> Url: https://github.com/boostorg/url/pull/587#issuecomment-1272697405  

GCOVR code coverage report [https://587.url.prtest.cppalliance.org/gcovr/index.html](https://587.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://587.url.prtest.cppalliance.org/genhtml/index.html](https://587.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-10 01:50:17 UTC  
> Updated_at: 2022-10-10 23:07:03 UTC  
> Url: https://github.com/boostorg/url/pull/587#issuecomment-1272700347  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/587?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#587](https://codecov.io/gh/boostorg/url/pull/587?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (abf70b1) into [develop](https://codecov.io/gh/boostorg/url/commit/d05447f52521daba97c0d5142a5f2f20e804122e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d05447f) will **decrease** coverage by `0.00%`.  
> The diff coverage is `96.75%`.  
  
> :exclamation: Current head abf70b1 differs from pull request most recent head 251855b. Consider uploading reports for the commit 251855b to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/587/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/587?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #587      +/-   ##  
===========================================  
- Coverage    96.81%   96.80%   -0.01%       
===========================================  
  Files          139      139                
  Lines         6688     6672      -16       
===========================================  
- Hits          6475     6459      -16       
  Misses         213      213                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/587?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/encode.hpp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2VuY29kZS5ocHA=) | `73.39% <85.00%> (-8.43%)` | :arrow_down: |  
| [include/boost/url/impl/encode.hpp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lbmNvZGUuaHBw) | `96.96% <96.87%> (-3.04%)` | :arrow_down: |  
| [include/boost/url/detail/impl/any\_params\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3BhcmFtc19pdGVyLmlwcA==) | `99.41% <100.00%> (ø)` | |  
| [...nclude/boost/url/detail/impl/any\_segments\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3NlZ21lbnRzX2l0ZXIuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/decode.hpp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvZGVjb2RlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/decode.ipp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvZGVjb2RlLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/normalize.ipp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvbm9ybWFsaXplLmlwcA==) | `99.36% <100.00%> (ø)` | |  
| [...lude/boost/url/detail/impl/remove\_dot\_segments.ipp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcmVtb3ZlX2RvdF9zZWdtZW50cy5pcHA=) | `99.30% <100.00%> (ø)` | |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `79.20% <100.00%> (ø)` | |  
| [include/boost/url/impl/decode.hpp](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGUuaHBw) | `100.00% <100.00%> (+20.00%)` | :arrow_up: |  
| ... and [5 more](https://codecov.io/gh/boostorg/url/pull/587/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/587?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/587?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d05447f...251855b](https://codecov.io/gh/boostorg/url/pull/587?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-10 20:26:45 UTC  
> Url: https://github.com/boostorg/url/pull/587#issuecomment-1273787254  

An automated preview of the documentation is available at [https://587.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://587.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-10 20:28:52 UTC  
> Url: https://github.com/boostorg/url/pull/587#issuecomment-1273790374  

GCOVR code coverage report [https://587.url.prtest.cppalliance.org/gcovr/index.html](https://587.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://587.url.prtest.cppalliance.org/genhtml/index.html](https://587.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 5 [Commented]

> Username: alandefreitas  
> Created_at: 2022-10-10 20:31:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/587#pullrequestreview-1136498746  

📁 doc/javadoc.hpp

```diff
   8 | //
   9 | 
  10 |+ Correct hyphenation:
```

> Username: alandefreitas  
> Created_at: 2022-10-10 20:25:58 UTC  
> Updated_at: 2022-10-10 20:31:40 UTC  
> Url: https://github.com/boostorg/url/pull/587#discussion_r991617761  

🙂


📁 test/unit/encode.cpp

```diff
  53 |             BOOST_TEST(encoded_size(
  45 |-                 s, opt, test_chars{}) ==
  54 |+                 s, test_chars{}, opt) ==
```

> Username: alandefreitas  
> Created_at: 2022-10-10 20:31:24 UTC  
> Updated_at: 2022-10-10 20:31:40 UTC  
> Url: https://github.com/boostorg/url/pull/587#discussion_r991620777  

We're moving these again?  
  
https://github.com/boostorg/url/pull/565#discussion_r974825002


---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-10-10 23:16:45 UTC  
> Url: https://github.com/boostorg/url/pull/587#issuecomment-1273902211  

An automated preview of the documentation is available at [https://587.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://587.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-10-10 23:18:03 UTC  
> Url: https://github.com/boostorg/url/pull/587#issuecomment-1273902934  

GCOVR code coverage report [https://587.url.prtest.cppalliance.org/gcovr/index.html](https://587.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://587.url.prtest.cppalliance.org/genhtml/index.html](https://587.url.prtest.cppalliance.org/genhtml/index.html)

---
