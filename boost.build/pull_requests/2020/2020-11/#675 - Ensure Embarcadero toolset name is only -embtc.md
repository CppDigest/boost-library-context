# #675 Ensure Embarcadero toolset name is only "embtc" [Merged]

> Username: tanzislam  
> Created at: 2020-11-25 15:57:44 UTC  
> Updated at: 2021-10-02 21:01:25 UTC  
> Merged at: 2020-12-02 15:44:47 UTC  
> Closed at: 2020-12-02 15:44:47 UTC  
> Url: https://github.com/boostorg/build/pull/675  

This change, along with boostorg/config#346, fixes the expected library names in a versioned-layout build.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-11-25 16:24:32 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-733810264  

The actual toolset for the Embarcadero C++ clang-based compiler, using clang command line parameters, is embarcadero.jam and not borland.jam.

---

## Comment 2

> Username: tanzislam  
> Created_at: 2020-11-25 16:51:23 UTC  
> Updated_at: 2020-11-25 16:51:58 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-733825594  

I can change the name from `bcb` to `embtc` (or similar), but that would mean:  
  
 - **either:** enlisting "embtc" [here](https://github.com/boostorg/build/blob/develop/src/tools/common.jam#L996-L1002) to avoid adding the version to the toolset name.  
 - **or:** having to maintain per-version toolset names in [`auto_link.hpp`](https://github.com/boostorg/config/blob/develop/include/boost/config/auto_link.hpp#L190-L197), similar to the MSVC cases above.  
  
What do you think?

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-11-25 17:52:29 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-733858798  

I think the embarcadero toolset should be different, with 'embtc' in both places. I do not understand the purpose of per-version toolset names.

---

## Comment 4

> Username: tanzislam  
> Created_at: 2020-11-25 18:51:00 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-733890294  

>I do not understand the purpose of per-version toolset names.  
  
It is to avoid any binary-incompatibility introduced between compiler releases, similar to the incompatibility between the classic and Clang-based compilers. Ideally users should recompile Boost with the compiler version they are using for their application code (especially when they are upgrading to a newer compiler release), and the "versioned" naming layout tries to protect against users forgetting to do so.  
  
For now, I avoided encoding the release version in the toolset name, as C++Builder seems to be only community-supported, so having to update `auto_link.hpp` with each C++Builder release will become a maintenance chore.

---

## Comment 5

> Username: tanzislam  
> Created_at: 2020-12-02 14:58:33 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-737283079  

@eldiener Does this look good to you? Since you're listed as a reviewer, your approval would most likely be needed before the maintainer(s) of the two repositories can decide when to merge these PRs into "develop" and later "master" for release.

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-12-02 15:39:31 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-737310575  

Looks fine to me.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2020-12-02 15:43:51 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-737313384  

Thanks @eldiener for taking the time to review.

---

## Comment 8

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:24 UTC  
> Url: https://github.com/boostorg/build/pull/675#issuecomment-932819912  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
