# #2560 basic_fields handles initializer-lists. [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-11-07 03:44:42 UTC  
> Updated at: 2022-12-20 07:08:20 UTC  
> Closed at: 2022-12-20 07:08:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2560  

This is very convenient for my requests library that (for now) uses the fields structure directly.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-11-07 04:43:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2560#issuecomment-1305073007  

I don't think we should keep adding stuff like this, and also we are in the release cycle so it is too late to add a new API

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-11-07 04:57:23 UTC  
> Updated_at: 2022-11-07 05:08:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2560#issuecomment-1305079686  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2560?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2560](https://codecov.io/gh/boostorg/beast/pull/2560?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9897193) into [develop](https://codecov.io/gh/boostorg/beast/commit/b986b3b1b0dbd96e4426d29afa3da8f77c3b4da8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b986b3b) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2560/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2560?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2560      +/-   ##  
===========================================  
- Coverage    94.73%   94.73%   -0.01%       
===========================================  
  Files          154      154                
  Lines        12027    12043      +16       
===========================================  
+ Hits         11394    11409      +15       
- Misses         633      634       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2560?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2560/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2560/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmhwcA==) | `97.30% <100.00%> (+0.08%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2560/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.06% <0.00%> (-0.85%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2560?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2560?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b986b3b...9897193](https://codecov.io/gh/boostorg/beast/pull/2560?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2022-11-10 04:14:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2560#issuecomment-1309745601  

It is meant for 1.82.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-11-10 05:59:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2560#issuecomment-1309814215  

You should write a free function `init_fields` that lets you construct with an initializer-list, keep it in your library, and not add it to beast

---
