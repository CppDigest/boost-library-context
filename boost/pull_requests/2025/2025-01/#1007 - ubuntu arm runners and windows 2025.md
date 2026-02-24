# #1007 ubuntu arm runners and windows 2025 [Closed]

> Username: johnnynunez  
> Created at: 2025-01-31 11:43:54 UTC  
> Updated at: 2025-02-01 19:02:47 UTC  
> Closed at: 2025-02-01 18:58:07 UTC  
> Url: https://github.com/boostorg/boost/pull/1007  

ubuntu arm runners and windows 2025

---

## Comment 1

> Username: sdarwin  
> Created_at: 2025-01-31 13:57:32 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2627418181  

... you are modifying, in addition to CI, the main gitmodules file to point to another org? that would block merging this PR

---

## Comment 2

> Username: johnnynunez  
> Created_at: 2025-01-31 17:07:51 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2627828329  

> ... you are modifying, in addition to CI, the main gitmodules file to point to another org? that would block merging this PR  
  
Done! Revert

---

## Comment 3

> Username: sdarwin  
> Created_at: 2025-01-31 17:27:14 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2627862311  

Thanks. Investigating github actions it seems `ubuntu-24.04-arm` is not automatically supported. In the Organization settings you have to create a hosted github runner.  That requires an Enterprise or Teams subscription and per-seat billing.     
Those modifications would need to be made in the Github account before continuing.

---

## Comment 4

> Username: johnnynunez  
> Created_at: 2025-01-31 17:54:32 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2627976555  

> Thanks. Investigating github actions it seems `ubuntu-24.04-arm` is not automatically supported. In the Organization settings you have to create a hosted github runner. That requires an Enterprise or Teams subscription and per-seat billing. Those modifications would need to be made in the Github account before continuing.  
  
it is supported for public repositories: https://github.blog/changelog/2025-01-16-linux-arm64-hosted-runners-now-available-for-free-in-public-repositories-public-preview/

---

## Comment 5

> Username: sdarwin  
> Created_at: 2025-01-31 19:07:33 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2628104192  

> Linux arm64 hosted runners now available for free in public repositories (Public Preview) January 16, 2025  
  
@pdimov

---

## Comment 6

> Username: johnnynunez  
> Created_at: 2025-01-31 19:08:40 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2628105797  

> > Linux arm64 hosted runners now available for free in public repositories (Public Preview) January 16, 2025  
>   
> @pdimov  
  
also windows arm is coming in the following weeks: https://github.com/github/roadmap/issues/1098

---

## Comment 7

> Username: johnnynunez  
> Created_at: 2025-01-31 19:09:21 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2628106823  

I'm trying to integrate all to arm, because, cuda arm laptops, digits, jetson, qualcom arm etc is coming more and more devices with arm

---

## Comment 8

> Username: pdimov  
> Created_at: 2025-02-01 18:58:07 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2629070565  

This PR is against `master`, but needs to be done against `develop`. Changing the base branch using the Github interface didn't work because our branches are too divergent. I'd suggest opening a separate PR against develop (without the .gitmodules changes.)

---

## Comment 9

> Username: johnnynunez  
> Created_at: 2025-02-01 19:02:46 UTC  
> Url: https://github.com/boostorg/boost/pull/1007#issuecomment-2629072253  

> This PR is against `master`, but needs to be done against `develop`. Changing the base branch using the Github interface didn't work because our branches are too divergent. I'd suggest opening a separate PR against develop (without the .gitmodules changes.)  
  
done https://github.com/boostorg/boost/pull/1008

---
