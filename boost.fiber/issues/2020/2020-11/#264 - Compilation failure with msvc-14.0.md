# #264 - Compilation failure with msvc-14.0 [Closed]

> Username: pdimov  
> Created at: 2020-11-07 23:22:34 UTC  
> Updated at: 2020-11-10 09:28:09 UTC  
> Closed at: 2020-11-10 09:28:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/264  

```  
compile-c-c++ bin.v2\libs\fiber\build\msvc-14.0\release\address-model-64\link-static\threading-multi\waker.obj  
waker.cpp  
.\boost/fiber/detail/spinlock_ttas_adaptive.hpp(48): error C2039: 'min': is not a member of 'std'  
```

---

## Comment 1

> Username: olk  
> Created at: 2020-11-08 17:36:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/264#issuecomment-723641863  

Could you fetch the commit and test it again, please.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-11-08 17:58:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/264#issuecomment-723644394  

This commit fixes the link=static build under msvc-14.0 (link=shared is still affected by the Intrusive issue.)  
  
Don't merge it to master yet though, because master is frozen; wait for @mclow 's permission.

---

## Comment 3

> Username: olk  
> Created at: 2020-11-10 09:28:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/264#issuecomment-724579253  

ty
