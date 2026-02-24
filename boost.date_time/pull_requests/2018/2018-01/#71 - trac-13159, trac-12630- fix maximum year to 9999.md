# #71 trac-13159, trac-12630: fix maximum year to 9999 [Merged]

> Username: jeking3  
> Created at: 2018-01-18 13:36:17 UTC  
> Updated at: 2018-01-24 14:44:12 UTC  
> Merged at: 2018-01-24 14:44:05 UTC  
> Closed at: 2018-01-24 14:44:05 UTC  
> Url: https://github.com/boostorg/date_time/pull/71  

#### Background ####  
https://svn.boost.org/trac10/ticket/13159  
https://svn.boost.org/trac10/ticket/12630  
  
#### Notes ####  
The documentation already specifies that the maximum year is 9999 so there are no documentation changes.  This pull request fixes the code to match the documentation.  
  
Why it allowed 10000 is beyond me, but it didn't make much sense.  10001 was not allowed for example.  Given year handling is all documented as "YYYY" there's no reason to allow 10000 to slip through.  It simply looks like there was a poor decision made in this area that this pull request corrects.

---

## Review 1 [Approved]

> Username: pdimov  
> Created_at: 2018-01-22 17:24:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/71#pullrequestreview-90555941  

Looks sensible.

---

## Comment 2

> Username: mclow  
> Created_at: 2018-01-22 21:58:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359581307  

LGTM.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-01-23 01:43:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359641965  

Is Boost.Format really needed in the tests? Let's keep the dependencies low, if possible.

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-01-23 01:47:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359642645  

Other than that, looks good to me.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-01-23 02:07:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359647559  

@Lastique thanks, I will take a look at that.  I recently (today) was able to remove Boost.Format's dependency on Boost.Test for our build, so Boost.Test can use Boost.Format now... that should make some folks happy I hope.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2018-01-23 15:01:06 UTC  
> Updated_at: 2018-01-24 05:44:09 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359817221  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/71?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@960c959`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/71/graphs/tree.svg?width=650&height=150&token=nDoh7t8f6g&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/71?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #71   +/-   ##  
==========================================  
  Coverage           ?   93.77%             
==========================================  
  Files              ?       80             
  Lines              ?     4864             
  Branches           ?        0             
==========================================  
  Hits               ?     4561             
  Misses             ?      303             
  Partials           ?        0  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/71?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/gregorian/greg\_year.hpp](https://codecov.io/gh/boostorg/date_time/pull/71/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfeWVhci5ocHA=) | `100% <100%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/71?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/71?src=pr&el=footer). Last update [960c959...72c5151](https://codecov.io/gh/boostorg/date_time/pull/71?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-01-23 16:24:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359845817  

I removed the dependency on Boost.Format in the tests I added and I added a little tool that helped me find all the direct library dependencies.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-01-23 16:37:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359850515  

`boostdep` tells you the dependencies.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-01-23 16:38:29 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359850842  

And the report does, too: https://pdimov.github.io/boostdep-report/

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-01-23 23:00:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/71#issuecomment-359961097  

Sweet - thanks for that link!

---
