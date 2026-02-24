# #107 Add arm64 instruction set. [Closed]

> Username: fxb  
> Created at: 2015-11-19 11:04:52 UTC  
> Updated at: 2021-10-02 21:19:20 UTC  
> Closed at: 2019-02-23 15:32:03 UTC  
> Url: https://github.com/boostorg/build/pull/107  



---

## Comment 1

> Username: vprus  
> Created_at: 2015-11-19 18:17:05 UTC  
> Url: https://github.com/boostorg/build/pull/107#issuecomment-158144042  

Hi Felix,  
  
are you adding this new value to be used with gcc or msvc, or some other compiler? I was thinking that for gcc, it would be better to use architecture=arm + address-model=64 to select arm64 options, and possibly the same with msvc. What do you think?

---

## Comment 2

> Username: fxb  
> Created_at: 2015-11-20 10:51:54 UTC  
> Updated_at: 2015-11-20 10:53:17 UTC  
> Url: https://github.com/boostorg/build/pull/107#issuecomment-158357336  

Hi Vladimir,  
  
This is required to correctly pass "-arch arm64" to the assembler when using the "darwin" toolset. Without it, compiling Boost.Context for arm64 fails.  
  
Maybe a better solution is to make the "darwin" toolset map "architecture=arm" and "address-model=64" to the "-arch arm64" flag.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2015-11-20 15:08:20 UTC  
> Url: https://github.com/boostorg/build/pull/107#issuecomment-158426079  

You should just to add the following to darwin.jam:  
  
```  
toolset.flags darwin OPTIONS <address-model>64/<architecture>arm : "-arch arm64" ;  
```  
  
To have that flag added. Which is what Volodya meant by his comment.

---

## Comment 4

> Username: fxb  
> Created_at: 2015-11-24 16:02:52 UTC  
> Url: https://github.com/boostorg/build/pull/107#issuecomment-159314241  

Would that be under "Generic options" in darwin.jam? As long as it passes "-arch arm64" when invoking the assember, it should be fine. The reason I added the arm64 instruction set was that it was consistent with the existing way of adding the "-arch" flag in darwin.jam. Possibly setup-address-model should be refactored into the way you suggest?

---

## Comment 5

> Username: vprus  
> Created_at: 2015-12-14 07:13:48 UTC  
> Url: https://github.com/boostorg/build/pull/107#issuecomment-164365201  

It seems to me that yes, changing setup-address-model so that the arm case checks for address-model property, and uses either 'arm' or 'arm64' will be the cleanest solution.

---
