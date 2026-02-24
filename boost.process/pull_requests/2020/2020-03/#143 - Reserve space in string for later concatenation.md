# #143 Reserve space in string for later concatenation [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 06:57:51 UTC  
> Updated at: 2021-02-18 03:20:34 UTC  
> Closed at: 2021-02-18 03:20:32 UTC  
> Url: https://github.com/boostorg/process/pull/143  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 13:13:50 UTC  
> Url: https://github.com/boostorg/process/pull/143#issuecomment-631465650  

Looks like a good idea, but doesn't compile. Please note that I the official standard for this library is still C++11

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-05-20 18:02:58 UTC  
> Updated_at: 2020-05-21 17:42:12 UTC  
> Url: https://github.com/boostorg/process/pull/143#issuecomment-631634597  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/143?src=pr&el=h1) Report  
> Merging [#143](https://codecov.io/gh/boostorg/process/pull/143?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/04ab646f128674fe66871b092e3263e08aa41ca5&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/143/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/143?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #143   +/-   ##  
========================================  
  Coverage    85.10%   85.10%             
========================================  
  Files          110      110             
  Lines         2564     2564             
========================================  
  Hits          2182     2182             
  Misses         382      382             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/143?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/environment.hpp](https://codecov.io/gh/boostorg/process/pull/143/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vudmlyb25tZW50LmhwcA==) | `95.12% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/143?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/143?src=pr&el=footer). Last update [04ab646...de79c98](https://codecov.io/gh/boostorg/process/pull/143?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: jonesmz  
> Created_at: 2020-05-21 09:21:27 UTC  
> Url: https://github.com/boostorg/process/pull/143#issuecomment-631981766  

I changed it to no longer require auto parameters in lambdas.  
  
Should compile with c++11 now.

---

## Comment 4

> Username: jonesmz  
> Created_at: 2020-05-21 09:22:50 UTC  
> Url: https://github.com/boostorg/process/pull/143#issuecomment-631982340  

Your continuous integration is broken.  
  
apt-get install failed  
The command "sudo -E apt-get -yq --no-install-suggests --no-install-recommends $(travis_apt_get_options) install valgrind python-yaml gcc-5 g++-5 clang" failed and exited with 100 during .

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2020-05-21 10:39:54 UTC  
> Url: https://github.com/boostorg/process/pull/143#issuecomment-632014984  

This is still C++14: `[first = &value.front()]`.

---

## Comment 6

> Username: jonesmz  
> Created_at: 2020-05-21 17:37:16 UTC  
> Url: https://github.com/boostorg/process/pull/143#issuecomment-632244347  

Fixed

---

## Comment 7

> Username: jonesmz  
> Created_at: 2021-02-18 03:20:32 UTC  
> Url: https://github.com/boostorg/process/pull/143#issuecomment-781017194  

it's been 11 months since i opened this PR. I'm closing it and deleting my fork of boost process.

---
