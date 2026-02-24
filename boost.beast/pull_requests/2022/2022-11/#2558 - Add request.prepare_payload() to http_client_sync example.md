# #2558 Add request.prepare_payload() to http_client_sync example [Closed]

> Username: meastp  
> Created at: 2022-11-01 21:50:14 UTC  
> Updated at: 2023-12-27 17:27:16 UTC  
> Closed at: 2023-12-27 17:27:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2558  

Even though the string_body is empty it should call prepare_payload to avoid an easy mistake for anyone reusing the example,  
  
This is identical in the other client examples as well. If acceptet, this PR could be amended, or I can make similar PRs.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-11-01 22:19:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2558#issuecomment-1299301299  

![pullrequest](https://2558.beast.tracing.cppalliance.org/pullrequest-43a1e331.png)  
Timeline tracing charts: [https://2558.beast.tracing.cppalliance.org/index.html](https://2558.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-11-01 22:41:26 UTC  
> Updated_at: 2022-11-02 03:37:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2558#issuecomment-1299318325  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2558?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2558](https://codecov.io/gh/boostorg/beast/pull/2558?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a6a8891) into [develop](https://codecov.io/gh/boostorg/beast/commit/b986b3b1b0dbd96e4426d29afa3da8f77c3b4da8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b986b3b) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2558/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2558?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2558      +/-   ##  
===========================================  
- Coverage    94.73%   94.72%   -0.01%       
===========================================  
  Files          154      154                
  Lines        12027    12027                
===========================================  
- Hits         11394    11393       -1       
- Misses         633      634       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2558?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2558/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.06% <0.00%> (-0.85%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2558?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2558?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b986b3b...a6a8891](https://codecov.io/gh/boostorg/beast/pull/2558?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2022-11-02 04:09:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2558#issuecomment-1299538442  

Should't the `get` us an `empty-body` instead?

---

## Comment 4

> Username: meastp  
> Created_at: 2022-11-06 21:48:55 UTC  
> Updated_at: 2022-11-06 21:51:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2558#issuecomment-1304902317  

@klemens-morgenstern Well, it could (and perhaps should, since there is no body content), but then the documentation should provide an example with `string_body` as well, that does call `prepare_payload`, imho. :) It is unavoidable that users often start by copying the examples in the documentation, and providing an example with a body and prepare_payload would be valuable - or else the prepare_payload call will cause a bug for them after changing the body type from the example to string_body and adding some content.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-11-07 02:04:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2558#issuecomment-1304988346  

I agree that we should do something, I'm not exactly sure what.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2022-12-21 07:33:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2558#issuecomment-1360954457  

Please check #2598, which should be a better basis for copy-pasta. Although maybe we need to instead do a cook-book.

---

## Comment 7

> Username: ashtum  
> Created_at: 2023-12-27 17:27:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2558#issuecomment-1870495018  

Closing this pull request because calling `prepare_payload()` is not required in those examples, and adding it might cause confusion for readers.

---
