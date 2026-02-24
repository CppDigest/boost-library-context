# #2414 return statement to exit reading loop [Closed]

> Username: oerol  
> Created at: 2022-04-25 12:12:19 UTC  
> Updated at: 2022-04-26 21:44:12 UTC  
> Closed at: 2022-04-26 21:44:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2414  

I used this example to start building my WebSocket application, however, I noticed that upon removing the echoing in the `on_read` method, the server repeatedly throws errors without exiting the method.   
  
You can recreate this problem by replacing `async_write` with `do_read` and cause any error (i.e. client reloads the page).  
  
Adding a return statement here would make debugging for future changes much easier and it wouldn't harm the existing echoing function in any way.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-04-25 12:38:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1108517169  

![pullrequest](https://2414.beast.tracing.cppalliance.org/pullrequest-717d1b1c.png)  
Timeline tracing charts: [https://2414.beast.tracing.cppalliance.org/index.html](https://2414.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-04-25 13:04:30 UTC  
> Updated_at: 2022-04-25 13:07:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1108543187  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2414?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2414](https://codecov.io/gh/boostorg/beast/pull/2414?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2b3be85) into [develop](https://codecov.io/gh/boostorg/beast/commit/95d9587ea76613d2f48f1400851155a560f14177?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (95d9587) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2414/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2414?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2414   +/-   ##  
========================================  
  Coverage    94.69%   94.69%             
========================================  
  Files          149      149             
  Lines        11773    11773             
========================================  
  Hits         11149    11149             
  Misses         624      624             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2414?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2414?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [95d9587...2b3be85](https://codecov.io/gh/boostorg/beast/pull/2414?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-04-26 04:31:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1109318746  

This has to be squashed down to 1 commit

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-04-26 04:32:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1109319031  

I suppose returning on an error is more correct

---

## Comment 5

> Username: oerol  
> Created_at: 2022-04-26 05:21:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1109356310  

Is there anything I can do to turn this change into one single commit?

---

## Comment 6

> Username: sehe  
> Created_at: 2022-04-26 12:22:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1109728921  

In general `git rebase`. @vinniefalco If you want it merged after a squash, I'm happy to do so from the command line? Merge takes the `--squash` option that does it.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-04-26 16:59:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1110036554  

I prefer a linear history, except for the ultra-rare case where master and develop truly diverge (for example to commit a release patch to master after develop has new commits).

---

## Comment 8

> Username: sehe  
> Created_at: 2022-04-26 21:44:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2414#issuecomment-1110278624  

Merged manually 90e37ae4

---
