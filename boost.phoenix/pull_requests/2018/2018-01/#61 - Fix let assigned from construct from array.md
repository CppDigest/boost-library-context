# #61 Fix let assigned from construct from array [Merged]

> Username: Kojoley  
> Created at: 2018-01-02 18:28:08 UTC  
> Updated at: 2018-02-07 14:39:51 UTC  
> Merged at: 2018-02-07 14:39:51 UTC  
> Closed at: 2018-02-07 14:39:51 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61  

Fixes https://github.com/boostorg/phoenix/pull/58#issuecomment-354685010 problem.  
  
Note: `let(_a = construct<T[N]>(...))` is an invalid thing because you cannot return arrays. Some static assert may be placed for this usage.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-01-03 13:40:16 UTC  
> Updated_at: 2018-01-03 13:41:36 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61#issuecomment-355013905  

Disclaimer: I do not know if `A` can be a reference type in this context (actually I tried to remove `proto::detail::uncvref` and it was fine), but I remember that some old compilers are not fine with `A const&` if `A` is already a reference (and it should be done with  `add_const` + `add_reference`), but at least all tests passes on `msvc-10`.

---

## Comment 2

> Username: Flast  
> Created_at: 2018-01-24 04:50:12 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61#issuecomment-360020092  

Closing and reopening to re-create travis job.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-01-25 00:07:22 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61#issuecomment-360316758  

I have updated PR with `add_const` + `add_reference` for msvc-8.  
  
P.S. Is it normal that I had to add `-DBOOST_PHOENIX_NO_VARIADIC_EXPRESSION` to `wave.cfg` to generate the preprocessed files?

---

## Comment 4

> Username: Flast  
> Created_at: 2018-01-26 03:36:59 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61#issuecomment-360675679  

IMO, add proxy traits to `boost/phoenix/config.hpp` and switch its impl with `BOOST_WORKAROUND`, then use the proxy traits is better way to support buggy compiler.  
  
Anyway, PP-ing is hard for somebody (especially newbie contributor), so defining `BOOST_PHOENIX_DONT_USE_PREPROCESSED_FILES` while CI is helpful (I'll do).

---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-01-26 11:23:14 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61#issuecomment-360759990  

Actually it is not only msvc-8 problem, gcc in c++03 mode complains too https://travis-ci.org/boostorg/phoenix/builds/332629779  
  
> Anyway, PP-ing is hard for somebody (especially newbie contributor), so defining `BOOST_PHOENIX_DONT_USE_PREPROCESSED_FILES` while CI is helpful (I'll do).  
  
I do not see how it will fix problem that currently wave preprocessing goes with the wrong settings and that's why does not generate any output.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-01-26 18:54:02 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61#issuecomment-360872021  

I suspect the mac builds will take forever to end. You might want to restart them or, better, turn them off.

---

## Comment 7

> Username: Flast  
> Created_at: 2018-02-07 01:48:57 UTC  
> Url: https://github.com/boostorg/phoenix/pull/61#issuecomment-363628396  

Sorry for review delaying...  
  
> IMO, add proxy traits to boost/phoenix/config.hpp and switch its impl with BOOST_WORKAROUND, then use the proxy traits is better way to support buggy compiler.  
  
Oops, I mistaken you said for it needs two form `A0` and `add_ref...<A0>::type` separately. OK, your patch seems good.  
  
> P.S. Is it normal that I had to add `-DBOOST_PHOENIX_NO_VARIADIC_EXPRESSION` to wave.cfg to generate the preprocessed files?  
  
AFAIK, wave should be executed without c++11 feature flag, i.e. the macro should be defined implicitly.  
  
> Anyway, PP-ing is hard for somebody (especially newbie contributor), so defining BOOST_PHOENIX_DONT_USE_PREPROCESSED_FILES while CI is helpful (I'll do).  
  
I think requesting PP-ing inhibits developing cycle (especially newbie developer). Additionally, PP-ed PR is sometimes huge and makes human unreadable (hard to review) one... So, disabling use of PP-ed code while CI, makes contributor don't have to worry about that, is what I meant on that line.

---
