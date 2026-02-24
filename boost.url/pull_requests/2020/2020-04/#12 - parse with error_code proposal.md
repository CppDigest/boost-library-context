# #12 parse with error_code proposal [Closed]

> Username: syoliver  
> Created at: 2020-04-11 15:36:39 UTC  
> Updated at: 2021-09-14 04:45:47 UTC  
> Closed at: 2021-09-14 04:45:47 UTC  
> Url: https://github.com/boostorg/url/pull/12  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-14 05:46:40 UTC  
> Updated_at: 2020-05-15 00:11:04 UTC  
> Url: https://github.com/boostorg/url/pull/12#issuecomment-628401190  

# [Codecov](https://codecov.io/gh/vinniefalco/url/pull/12?src=pr&el=h1) Report  
> Merging [#12](https://codecov.io/gh/vinniefalco/url/pull/12?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/url/commit/7371e6a9e982995a0f2fbcf3a9dfc6a6641e4c40&el=desc) will **increase** coverage by `0.07%`.  
> The diff coverage is `96.87%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/url/pull/12/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi)](https://codecov.io/gh/vinniefalco/url/pull/12?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #12      +/-   ##  
===========================================  
+ Coverage    92.87%   92.94%   +0.07%       
===========================================  
  Files           15       15                
  Lines         2146     2170      +24       
===========================================  
+ Hits          1993     2017      +24       
  Misses         153      153                
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/url/pull/12?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/vinniefalco/url/pull/12/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `93.24% <95.00%> (+0.10%)` | :arrow_up: |  
| [include/boost/url/basic\_url.hpp](https://codecov.io/gh/vinniefalco/url/pull/12/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91cmwvYmFzaWNfdXJsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/vinniefalco/url/pull/12/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `93.30% <100.00%> (+0.12%)` | :arrow_up: |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/vinniefalco/url/pull/12/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `98.36% <100.00%> (+0.05%)` | :arrow_up: |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/vinniefalco/url/pull/12/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/url/pull/12?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/url/pull/12?src=pr&el=footer). Last update [7371e6a...ef28c8d](https://codecov.io/gh/vinniefalco/url/pull/12?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-09-14 04:45:47 UTC  
> Url: https://github.com/boostorg/url/pull/12#issuecomment-918797455  

This is done, but they are free functions which return a `url_view` (from which a `url` may optionally be constructed. See these functions:  
```  
parse_uri  
parse_uri_ref  
parse absolute_uri  
parse_relative_ref  
```  
  
Also see `parse_path` and `parse_query_params`.  
  
Docs here: https://master.url.cpp.al/

---
