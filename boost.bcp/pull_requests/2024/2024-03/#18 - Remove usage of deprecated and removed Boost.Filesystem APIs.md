# #18 Remove usage of deprecated and removed Boost.Filesystem APIs [Merged]

> Username: Lastique  
> Created at: 2024-03-24 11:51:05 UTC  
> Updated at: 2024-08-27 08:26:06 UTC  
> Merged at: 2024-04-29 18:00:36 UTC  
> Closed at: 2024-04-29 18:00:36 UTC  
> Url: https://github.com/boostorg/bcp/pull/18  



---

## Comment 1

> Username: Lastique  
> Created_at: 2024-03-24 14:26:04 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2016827084  

The `b2` crash in the CI is reported here: https://github.com/bfgroup/b2/issues/367.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-03-24 16:13:25 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2016857389  

Thanks for this!

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-16 15:11:29 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2059331106  

`bcp` doesn't seem to build in 1.85 because this PR hasn't been merged.

---

## Comment 4

> Username: Lastique  
> Created_at: 2024-04-16 15:35:51 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2059384721  

I suspect, it wasn't merged because of the b2 issue. I think, this can be merged despite that issue, if only to fix the build.  
  
Note: the first commit in this PR fixes the compilation, the second one updates the dependencies that should fix the CI once b2 is fixed.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-04-16 15:37:55 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2059388977  

The b2 issue is unrelated. Not merging this won't fix it.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-04-17 08:30:00 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2060699083  

My bad.  I didn't merge because we can't tell if the patch is really working until the tests run... (I appreciate it compiles, but that's not quite the same thing as working).

---

## Comment 7

> Username: pdimov  
> Created_at: 2024-04-17 09:28:26 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2060818445  

Rene says it doesn't crash for him, so let's close and reopen and see if that helps. :-)

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-04-17 09:39:21 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2060839017  

Nope, still crashes. @grafikrobot

---

## Comment 9

> Username: Lastique  
> Created_at: 2024-04-17 10:42:49 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2060963519  

Note that the CI uses the b2 version that was released in Boost.

---

## Comment 10

> Username: grafikrobot  
> Created_at: 2024-04-19 14:35:52 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2066718466  

The B2 problem was not technically a B2 problem. It was actually a Predef problem. But only the Boost Predef, not the independent Hash Predef. But also none of the following will make the BCP tests work. As BCP is not copying the correct predef/tools/check/predef.jam file. The following changes will prevent the stack overflow crash:  
  
* https://github.com/bfgroup/b2/commit/e249722ccd20521e4c560a5afdf1a36828b1ea37 (B2 change that addresses the general stack overflow problem)  
* https://github.com/boostorg/predef/commit/9aca7f5b609a731106a6d70e8dca9a4196dca968  
* https://github.com/boostorg/vmd/pull/14  
* https://github.com/boostorg/test/pull/414  
* https://github.com/boostorg/test/pull/413  
* https://github.com/boostorg/math/pull/1122  
* https://github.com/boostorg/locale/pull/227  
  
And saying again.. None of those changes will make this PR build. As the tip of the problem is that BCP doesn't copy predef/tools/check/predef.jam.

---

## Comment 11

> Username: Lastique  
> Created_at: 2024-04-19 15:21:49 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2066800637  

Thanks, Rene. I've added the dependency on libs/predef/tools/check in addition to libs/predef/check.

---

## Comment 12

> Username: Lastique  
> Created_at: 2024-04-29 17:37:12 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2083288902  

@jzmaddock Could you merge this, please?

---

## Comment 13

> Username: kwaegel  
> Created_at: 2024-08-27 00:16:49 UTC  
> Updated_at: 2024-08-27 00:42:20 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2311329974  

Is this fixed, or is it still waiting for some of the predef PRs to get merged? I'm still seeing the same error messages trying to run BCP from the Boost 1.86.0 tarball release.  
  
[edit] There is another ticket open about this, saying the fix has been merged to the `develop` branch but not the `master` branch that the boost repo tracks: https://github.com/boostorg/bcp/pull/18

---

## Comment 14

> Username: Lastique  
> Created_at: 2024-08-27 08:26:05 UTC  
> Url: https://github.com/boostorg/bcp/pull/18#issuecomment-2311880441  

This wasn't merged to master before 1.86.0, so it didn't make it to the release. It is merged now, so it should make it into the next one.

---
