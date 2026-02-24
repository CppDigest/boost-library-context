# #224 Improve special function support [Merged]

> Username: jzmaddock  
> Created at: 2020-04-22 11:05:53 UTC  
> Updated at: 2021-01-30 16:10:17 UTC  
> Merged at: 2020-05-02 09:47:47 UTC  
> Closed at: 2020-05-02 09:47:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/224  

And hook up testing to circle-ci as there are unnoticed regressions.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-04-22 17:27:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/224#issuecomment-617919954  

> I've been meaning to ask if we have a policy on the order of system include and Boost includes.  
  
Not really, and it *shouldn't* make any difference - in this case I have a weird problem that's only showing up in C++17 and later that was fixed by re-ordering things.  At some point I'll try and investigate...

---
