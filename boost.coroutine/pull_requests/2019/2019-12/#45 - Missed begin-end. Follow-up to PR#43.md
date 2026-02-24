# #45 Missed begin/end. Follow-up to PR#43 [Closed]

> Username: Kojoley  
> Created at: 2019-12-01 22:40:09 UTC  
> Updated at: 2020-04-21 12:36:13 UTC  
> Closed at: 2020-03-30 15:17:39 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45  

I did not notice that begin/end indirectly uses range_begin/range_end.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-12-01 22:43:30 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-560169099  

Oh, you already reverted #43... This should fix https://github.com/boostorg/coroutine/issues/44 so you can revert the revert.

---

## Comment 2

> Username: mellery451  
> Created_at: 2020-02-18 22:53:32 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-587945049  

currently seeing this error with boost 1.72 (via asio). Is this fix planned for 1.73 and/or is there a workaround in the meantime?

---

## Comment 3

> Username: olk  
> Created_at: 2020-02-21 14:14:15 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-589670198  

> currently seeing this error with boost 1.72 (via asio). Is this fix planned for 1.73 and/or is there a workaround in the meantime?  
  
release notes address this issue (patch): https://www.boost.org/users/history/version_1_72_0.html

---

## Comment 4

> Username: mellery451  
> Created_at: 2020-02-22 00:11:21 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-589889142  

@olk thanks for that information. Turns out the patch in release notes does not fix the `begin/end` issue here, but I'll look around for another patch that does.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-03-30 14:49:42 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-606046537  

Beast's _master_ branch is broken, and it looks related to the range begin/end:  
https://travis-ci.org/github/boostorg/beast/builds/668510892  
  
Beast _develop_ seems fine.

---

## Comment 6

> Username: olk  
> Created_at: 2020-03-30 15:17:11 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-606062703  

I've had to revert a commit twice (don't know why the first one didn't remove the problematic lines) - should be fixed now.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2020-03-30 17:42:57 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-606142846  

I am sorry for formerly opening a not well tested PR. I did test it but only on Windows with MSVC and Clang (which seems to disable two-phase lookup to mimic MSVC) and sadly the repository has no CI. However I do not understand why closed this PR.

---

## Comment 8

> Username: omegacoleman  
> Created_at: 2020-04-21 12:36:13 UTC  
> Url: https://github.com/boostorg/coroutine/pull/45#issuecomment-617152171  

confirmed missing boost::begin/end bug on 172, fixed after manually applying this patch

---
