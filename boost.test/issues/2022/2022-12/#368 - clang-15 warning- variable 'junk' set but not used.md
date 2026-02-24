# #368 - clang-15 warning: variable 'junk' set but not used [Closed]

> Username: k15tfu  
> Created at: 2022-12-21 13:38:37 UTC  
> Updated at: 2023-02-06 20:35:28 UTC  
> Closed at: 2023-02-06 20:35:27 UTC  
> Url: https://github.com/boostorg/test/issues/368  

Hi!  
  
In file boost/test/impl/debug.ipp:  
```  
boost/test/impl/debug.ipp:674:25: error: variable 'junk' set but not used [-Werror,-Wunused-but-set-variable]  
    int                 junk;  
                        ^  
1 error generated.  
```

---

## Comment 1

> Username: mborland  
> Created at: 2023-02-06 20:35:27 UTC  
> Url: https://github.com/boostorg/test/issues/368#issuecomment-1419714915  

Fixed by https://github.com/boostorg/test/pull/372
