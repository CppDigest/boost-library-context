# #38 - GCC 11 rejects constructor syntax [Closed]

> Username: mgaunard  
> Created at: 2021-05-06 00:31:46 UTC  
> Updated at: 2021-05-15 00:54:03 UTC  
> Closed at: 2021-05-15 00:54:03 UTC  
> Url: https://github.com/boostorg/msm/issues/38  

For constructors of template classes, GCC 11 is stricter than previous versions and considers that repeating the template argument list is invalid syntax.  
This means that Boost.MSM doesn't build with that compiler.  
  
For example, In state_machine.hpp, the default constructor `state_machine<A0,A1,A2,A3,A4 >()` should just be declared as `state_machine()`, and likewise for all the others.

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2021-05-14 14:48:50 UTC  
> Updated at: 2021-05-14 14:49:02 UTC  
> Url: https://github.com/boostorg/msm/issues/38#issuecomment-841291266  

FYI, I submitted https://github.com/boostorg/msm/pull/26 to fix it months ago. However this library seems abandoned since several years already...

---

## Comment 2

> Username: mgaunard  
> Created at: 2021-05-14 15:36:55 UTC  
> Url: https://github.com/boostorg/msm/issues/38#issuecomment-841320285  

Yeah, there are a bunch of libraries like that, usually maintained collectively by some committee within Boost.  
Can try pinging @pdimov who did all recent changes to this repo.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-05-15 00:54:03 UTC  
> Url: https://github.com/boostorg/msm/issues/38#issuecomment-841574380  

Resolved by #26.
