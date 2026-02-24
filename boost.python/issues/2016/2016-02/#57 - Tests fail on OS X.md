# #57 - Tests fail on OS X [Open]

> Username: spkersten  
> Created at: 2016-02-27 15:22:59 UTC  
> Updated at: 2016-08-07 05:31:22 UTC  
> Url: https://github.com/boostorg/python/issues/57  

On OS X (10.11.3) the tests fail because a relative rpath is not allowed:  
  
```  
running...  
Traceback (most recent call last):  
  File "crossmod_exception.py", line 7, in <module>  
    import crossmod_exception_a  
ImportError: dlopen(/Users/spkersten/Development/boost-github/boost/bin.v2/libs/python/test/crossmod_exception.test/darwin-4.2.1/debug/crossmod_exception_a.so, 2): Library not loaded: libboost_python.dylib  
  Referenced from: /Users/spkersten/Development/boost-github/boost/bin.v2/libs/python/test/crossmod_exception.test/darwin-4.2.1/debug/crossmod_exception_a.so  
  Reason: unsafe use of relative rpath libboost_python.dylib in /Users/spkersten/Development/boost-github/boost/bin.v2/libs/python/test/crossmod_exception.test/darwin-4.2.1/debug/crossmod_exception_a.so with restricted binary  
```  
  
I'm running the tests by executing bjam in boost/libs/python/test.

---

## Comment 1

> Username: mirzaelahi  
> Created at: 2016-03-22 06:08:01 UTC  
> Url: https://github.com/boostorg/python/issues/57#issuecomment-199651311  

faced the same problem
