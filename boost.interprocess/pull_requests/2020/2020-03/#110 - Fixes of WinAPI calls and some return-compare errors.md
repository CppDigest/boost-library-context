# #110 Fixes of WinAPI calls and some return-compare errors [Merged]

> Username: oktonion  
> Created at: 2020-03-02 08:05:21 UTC  
> Updated at: 2021-02-03 11:24:35 UTC  
> Merged at: 2021-02-02 14:10:05 UTC  
> Closed at: 2021-02-02 14:10:06 UTC  
> Url: https://github.com/boostorg/interprocess/pull/110  



---

## Comment 1

> Username: oktonion  
> Created_at: 2020-03-02 08:19:15 UTC  
> Url: https://github.com/boostorg/interprocess/pull/110#issuecomment-593276779  

Oops, my bad for the first fix (7935630), I was wrong. Redone in 6586572.

---

## Comment 2

> Username: oktonion  
> Created_at: 2021-02-02 14:35:20 UTC  
> Url: https://github.com/boostorg/interprocess/pull/110#issuecomment-771677426  

what about https://github.com/boostorg/interprocess/pull/110/commits/771e849faecdd4136dcd2fcea8c463144a1cde2f ?

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2021-02-03 10:30:26 UTC  
> Url: https://github.com/boostorg/interprocess/pull/110#issuecomment-772404302  

Hi, for the moment I prefer not to apply it as usually those wrappers maintain the argument count of the Windows API. Thanks again.

---

## Comment 4

> Username: oktonion  
> Created_at: 2021-02-03 11:24:35 UTC  
> Url: https://github.com/boostorg/interprocess/pull/110#issuecomment-772436981  

>   
>   
> Hi, for the moment I prefer not to apply it as usually those wrappers maintain the argument count of the Windows API. Thanks again.  
  
NP, just pointing out that those wrapped WinAPI functions "reserved" params could be changed in future by MS and my fix does not change wrapper params count. However if some boost codebase end-user has passed (by mistake) reserved param (anything except zero) to the wrappers my fix would prevent misuse of WinAPI function call when MS will decide this parameter appliance.  
  
In my view this is safer but it is up to maintainers to decide what is right approach there. Thanks for review anyway. =)

---
