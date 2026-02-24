# #572 - Drone Failures [Closed]

> Username: mborland  
> Created at: 2021-03-16 17:38:57 UTC  
> Updated at: 2021-03-17 18:33:20 UTC  
> Closed at: 2021-03-17 18:33:20 UTC  
> Url: https://github.com/boostorg/math/issues/572  

It looks like clang-9 and clang-10 have both recently started failing in drone with the following message:  
  
```  
The following packages have unmet dependencies:  
 clang-9 : Depends: libclang-common-9-dev (= 1:9~+20210314105943+c1a0a213378a-1~exp1~20210314220516.107) but it is not going to be installed  
E: Unable to correct problems, you have held broken packages.  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-03-16 18:49:08 UTC  
> Url: https://github.com/boostorg/math/issues/572#issuecomment-800518902  

See https://github.com/boostorg/math/pull/573.

---

## Comment 2

> Username: mborland  
> Created at: 2021-03-17 18:33:20 UTC  
> Url: https://github.com/boostorg/math/issues/572#issuecomment-801317221  

Looks to be resolved in newer runs. Thank you.
