# #87 - tests for python3 - what about to remove useless dependence on 'past.builtins' ? [Closed]

> Username: vmurashev  
> Created at: 2016-09-24 14:34:28 UTC  
> Updated at: 2016-09-29 03:45:29 UTC  
> Closed at: 2016-09-29 03:45:29 UTC  
> Url: https://github.com/boostorg/python/issues/87  

Rationale:  
'past.builtins' is not a part of stock python modules, as result it's difficult to run boost-python tests in limited environments (like Android) at the same time it is the one line only to fix it -  
Code like  
if (sys.version_info.major >= 3):  
    from past.builtins import long  
  
Can be easily replaced with:  
if (sys.version_info.major >= 3):  
    long = int

---

## Comment 1

> Username: vmurashev  
> Created at: 2016-09-24 14:52:08 UTC  
> Url: https://github.com/boostorg/python/issues/87#issuecomment-249368617  

patch suggested - see pull request 88

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-09-29 03:45:29 UTC  
> Url: https://github.com/boostorg/python/issues/87#issuecomment-250363466  

Much simpler indeed. Merged, thanks !
