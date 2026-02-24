# #2017 simplify openssl on windows [Closed]

> Username: sdarwin  
> Created at: 2020-07-14 19:07:31 UTC  
> Updated at: 2020-07-24 12:06:56 UTC  
> Closed at: 2020-07-24 12:06:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2017  

For your consideration.    
I don't believe this creates any new requirement on OpenSSL which wasn't there before. The goal is only to make including the libraries easier. Let me know what you think.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-07-14 19:14:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2017#issuecomment-658362593  

An automated preview of the documentation is available at [http://2017.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://2017.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-07-14 19:22:40 UTC  
> Updated_at: 2020-07-14 22:24:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2017#issuecomment-658366569  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2017?src=pr&el=h1) Report  
> Merging [#2017](https://codecov.io/gh/boostorg/beast/pull/2017?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3486e9cb18aa39b392e07031a33e65b1792fbccf&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2017/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2017?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2017      +/-   ##  
===========================================  
- Coverage    95.09%   95.08%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11939    11939                
===========================================  
- Hits         11353    11352       -1       
- Misses         586      587       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2017?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2017/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2017/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.50% <0.00%> (-0.11%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2017/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0.00%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2017?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2017?src=pr&el=footer). Last update [3486e9c...7749843](https://codecov.io/gh/boostorg/beast/pull/2017?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-07-14 22:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2017#issuecomment-658444758  

An automated preview of the documentation is available at [http://2017.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://2017.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 4 [Approved]

> Username: madmongo1  
> Created_at: 2020-07-16 13:20:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/2017#pullrequestreview-449839764  

Tested. Approved.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-07-18 15:34:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2017#issuecomment-660499601  

Don't need punctuation in the first line of commit messages

---
