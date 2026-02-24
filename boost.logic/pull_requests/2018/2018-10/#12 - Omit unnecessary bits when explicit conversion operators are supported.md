# #12 Omit unnecessary bits when explicit conversion operators are supported [Merged]

> Username: jeking3  
> Created at: 2018-10-13 15:43:59 UTC  
> Updated at: 2018-10-16 19:04:48 UTC  
> Merged at: 2018-10-16 19:04:45 UTC  
> Closed at: 2018-10-16 19:04:45 UTC  
> Url: https://github.com/boostorg/logic/pull/12  

This continues #11 which continues #7.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-13 15:48:29 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-429552243  

`safe_bool` remains for environments that do not support explicit conversion operators (thus preventing unwanted numeric conversions).  This just removes unnecessary bits when explicit conversion operators are supported in the environment and adds a test.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2018-10-13 16:45:33 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-429556648  

I wanted to post this to #7 but it was (mostly) already mentioned in #11   
  
Anyway:  
  
@jeking3 could you test the following (add test cases if not existant):  
  
```  
tribool tb = boost::logic::tribool(boost::logic::indeterminate);  
bool b1 = tb; // Fail for at least C++11 and up  
bool b2 = static_cast<bool>(tb); // OK  
int i1 = tb; // Fail  
int i2 = tb + 1; // Fail  
int i3 = tb << 1; // Fail  
if(tb) b1 = !b1; //OK  
if(tb == true) b1 = !b1; //OK  
if(tb < 1) b1 = !b1; //Fail  
```  
  
Besides that this PR looks good to me. (I was one of the people against unconditional removal of safe bool which would allow int conversions)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-10-14 03:08:59 UTC  
> Updated_at: 2018-10-16 16:02:15 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-429592770  

# [Codecov](https://codecov.io/gh/boostorg/logic/pull/12?src=pr&el=h1) Report  
> Merging [#12](https://codecov.io/gh/boostorg/logic/pull/12?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/logic/commit/d76d7dc4d264f796ba85d7e7c6cfcad83eda7742?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/logic/pull/12/graphs/tree.svg?width=650&token=2RCqoPxHln&height=150&src=pr)](https://codecov.io/gh/boostorg/logic/pull/12?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #12   +/-   ##  
========================================  
  Coverage    63.35%   63.35%             
========================================  
  Files            2        2             
  Lines          131      131             
  Branches        69       69             
========================================  
  Hits            83       83             
  Misses           2        2             
  Partials        46       46  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/logic/pull/12?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/logic/tribool.hpp](https://codecov.io/gh/boostorg/logic/pull/12/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9sb2dpYy90cmlib29sLmhwcA==) | `91.07% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/logic/pull/12?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/logic/pull/12?src=pr&el=footer). Last update [d76d7dc...3345f25](https://codecov.io/gh/boostorg/logic/pull/12?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-16 12:20:16 UTC  
> Updated_at: 2018-10-16 12:20:40 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-430215682  

Expanding the CI build matrix found gcc-4.6 isn't handling the test re-enabled by @pdimov earlier this year, but Boost.Config claims the compiler has support.  See:  
  
https://travis-ci.org/boostorg/logic/jobs/442138010#L1108  
https://github.com/boostorg/logic/pull/12/files#diff-0f78196b9b0d3cb6cb11ba6edeb43e68R145

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-10-16 13:34:17 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-430239313  

I do not believe that any time should be spent getting versions of gcc below gcc-4.8 to work with any libraries using any c++11 features.

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-16 14:02:33 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-430249623  

It's either adding a BOOST_WORKAROUND here (to the condition enabling the constexpr test), or changing Config, and if we go by the path of least effort, we should choose the former.

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-10-16 15:05:31 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-430274097  

The only thing that concerns me is that in the last few days I've seen the need to add BOOST_WORKAROUND for gcc < 4.7 twice now.  Not certain why Boost.Config is claiming support for something that is pre-standard and (now provably, twice) incorrect.  I do understand changing the config macros have far wider implications however.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-10-16 15:08:37 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-430275376  

If you want to pursue this path, the procedure would be a PR against Config, which defines the defect macro in `gcc.hpp` appropriately for 4.6 and adds to its test the failing case.

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-10-16 18:10:25 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-430340390  

Any concerns with the content here for merge?

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-10-16 18:29:29 UTC  
> Url: https://github.com/boostorg/logic/pull/12#issuecomment-430347087  

Everything looks good to me.

---
