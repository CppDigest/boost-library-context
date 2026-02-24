# #953 - No install target for boost/math [Closed]

> Username: NAThompson  
> Created at: 2023-02-15 04:22:24 UTC  
> Updated at: 2023-02-16 00:43:26 UTC  
> Closed at: 2023-02-16 00:43:26 UTC  
> Url: https://github.com/boostorg/math/issues/953  

Steps to reproduce:  
  
```  
math/build$ cmake ../ -G Ninja  
math/build$ ninja  
math/build$ ninja install  
ninja: error: unknown target 'install', did you mean 'test/all'?  
```

---

## Comment 1

> Username: mborland  
> Created at: 2023-02-15 04:26:01 UTC  
> Url: https://github.com/boostorg/math/issues/953#issuecomment-1430739890  

Is this to install the standalone package?

---

## Comment 2

> Username: NAThompson  
> Created at: 2023-02-15 04:28:59 UTC  
> Url: https://github.com/boostorg/math/issues/953#issuecomment-1430741356  

@mborland : That was the goal: Put the standalone headers on the syspath.
