# #1556 Set parser status and flags even if body_limit_ has been reached [Closed]

> Username: medelh  
> Created at: 2019-04-04 17:39:05 UTC  
> Updated at: 2019-04-11 20:58:35 UTC  
> Closed at: 2019-04-11 20:58:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1556  

Parsing the header of a request that is larger than 1 MB prevents the setting of body_limit and reading the request body

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-04-04 17:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1556#issuecomment-479994813  

Seems reasonable, thanks! This will have to go into the develop branch for now, as the Boost 1.70 release is closed (and about to ship). After the release I can merge it to master, but it will not be available in the official Boost distribution until Boost 1.71.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-04-04 17:58:02 UTC  
> Updated_at: 2019-04-08 22:22:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1556#issuecomment-480000823  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1556?src=pr&el=h1) Report  
> Merging [#1556](https://codecov.io/gh/boostorg/beast/pull/1556?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/b701d72ddbed3b6cd39ea038e67c8dbf2e8e4e24?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `33.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1556/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1556?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1556   +/-   ##  
========================================  
  Coverage    92.63%   92.63%             
========================================  
  Files          150      150             
  Lines        12131    12131             
========================================  
  Hits         11237    11237             
  Misses         894      894  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1556?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/1556/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `93.55% <33.33%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1556?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1556?src=pr&el=footer). Last update [b701d72...5233260](https://codecov.io/gh/boostorg/beast/pull/1556?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2019-04-08 21:25:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1556#issuecomment-481014209  

@medelh your username and email are not configured locally (using `git config`) so the commit is not linked to your GitHub account, is this intended?

---

## Comment 4

> Username: medelh  
> Created_at: 2019-04-08 21:48:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1556#issuecomment-481021237  

> @medelh your username and email are not configured locally (using `git config`) so the commit is not linked to your GitHub account, is this intended?  
  
not really, it was by mistake. Can it still be set?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2019-04-08 21:59:55 UTC  
> Url: https://github.com/boostorg/beast/pull/1556#issuecomment-481024288  

> not really, it was by mistake. Can it still be set?  
  
Yes, it can - and I would prefer that you are properly credited for the contribution. You just need to use `git config` to set your username and email to match the credentials on your GitHub account, amend the commit locally, and then force-push it to your fork - this will update the pull request automatically.

---

## Comment 6

> Username: medelh  
> Created_at: 2019-04-08 22:10:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1556#issuecomment-481027004  

Many thanks!

---
