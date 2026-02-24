# #152 Cast month numbers to the correct type in date parsing map init [Merged]

> Username: Lastique  
> Created at: 2020-05-02 07:59:35 UTC  
> Updated at: 2020-05-04 16:43:45 UTC  
> Merged at: 2020-05-03 14:52:25 UTC  
> Closed at: 2020-05-03 14:52:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/152  

Since the map contains `unsigned short` for month numbers, it is more correct to explicitly cast constants to that type rather than `short`.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-02 10:19:36 UTC  
> Url: https://github.com/boostorg/date_time/pull/152#issuecomment-622931054  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/152?src=pr&el=h1) Report  
> Merging [#152](https://codecov.io/gh/boostorg/date_time/pull/152?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/d0b3c1da885cd6cb5e005f8381351e5386d058ba&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/152/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/152?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #152   +/-   ##  
========================================  
  Coverage    52.72%   52.72%             
========================================  
  Files           76       76             
  Lines         4491     4491             
  Branches      2243     2243             
========================================  
  Hits          2368     2368             
  Misses         391      391             
  Partials      1732     1732             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/152?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/date\_parsing.hpp](https://codecov.io/gh/boostorg/date_time/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9wYXJzaW5nLmhwcA==) | `33.01% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/152?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/152?src=pr&el=footer). Last update [d0b3c1d...9e181d5](https://codecov.io/gh/boostorg/date_time/pull/152?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mclow  
> Created_at: 2020-05-02 14:02:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/152#issuecomment-622958448  

That's amazingly ugly.   This is because of https://stackoverflow.com/questions/61552098/boost-1-73-log-library-regression, isn't it?  
  
I stand by my comment that this is the compiler being silly.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-05-02 15:03:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/152#issuecomment-622967538  

I totally agree that the compiler should not emit that warning. However, if we do the cast, we should cast to the right type.

---

## Comment 4

> Username: mclow  
> Created_at: 2020-05-02 20:25:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/152#issuecomment-623008786  

I concur 100%.   
IMHO, we should remove the casts and suppress the warning.

---

## Comment 5

> Username: JeffGarland  
> Created_at: 2020-05-03 15:09:12 UTC  
> Url: https://github.com/boostorg/date_time/pull/152#issuecomment-623124415  

although I agree the compiler is silly it's not worth more intellectual effort at this point so just merging it.

---

## Comment 6

> Username: mclow  
> Created_at: 2020-05-04 16:42:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/152#issuecomment-623574807  

I've spoken to a MS compiler dev, and he's opened a bug against MSVC for this behavior.

---

## Comment 7

> Username: JeffGarland  
> Created_at: 2020-05-04 16:43:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/152#issuecomment-623575598  

wow nice! anyway we can get notified when it is fixed?

---
