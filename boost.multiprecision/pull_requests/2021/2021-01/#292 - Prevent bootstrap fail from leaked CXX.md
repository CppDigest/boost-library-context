# #292 Prevent bootstrap fail from leaked CXX. [Closed]

> Username: grafikrobot  
> Created at: 2021-01-18 14:19:49 UTC  
> Updated at: 2021-01-18 15:39:44 UTC  
> Closed at: 2021-01-18 15:30:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/292  



---

## Comment 1

> Username: grafikrobot  
> Created_at: 2021-01-18 15:30:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/292#issuecomment-762320888  

Closing in preference of https://github.com/boostorg/boost/pull/466

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-18 15:31:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/292#issuecomment-762321497  

Thanks Rene: I've cancelled the travis/appveyor builds for this as they aren't effected by the fix, strangely I can find no trace a drone build for this, not even a failed one.  
  
Please note that the drone failures were different from travis - bootstrap of b2 is passing, it's the b2 headers step that's failing.  There are also a lot of similar scripts out there :(

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2021-01-18 15:36:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/292#issuecomment-762324052  

The b2 headers error is a fallout of the CXX being set. As ti guesses an empty toolset creating a bogus `project-config.jam`. Which is only apparent when b2 is first called. Regardless.. Fixing it in the `bootstrap.sh` instead resolves it for everyone and all CI's.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-01-18 15:39:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/292#issuecomment-762326052  

Got it!  Many thanks for tracking this down- it had me well stumped :(

---
