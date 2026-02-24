# #46 - "rename of continuation member functions" missed a spot [Closed]

> Username: Rastaban  
> Created at: 2017-03-09 18:50:35 UTC  
> Updated at: 2017-03-09 19:18:57 UTC  
> Closed at: 2017-03-09 19:18:57 UTC  
> Url: https://github.com/boostorg/context/issues/46  

We (the MSVC++ team) do a nightly build of the latest boost sources as a compiler "Real World Code" test.  We noticed a build break after the "rename of continuation member functions" change in test/test_callcc.cpp.   Line 281 still has a c(), this should probably be changed to c.resume().

---

## Comment 1

> Username: Rastaban  
> Created at: 2017-03-09 18:51:51 UTC  
> Url: https://github.com/boostorg/context/issues/46#issuecomment-285444040  

@olk could you take a look at this?  Thanks!

---

## Comment 2

> Username: olk  
> Created at: 2017-03-09 19:18:57 UTC  
> Url: https://github.com/boostorg/context/issues/46#issuecomment-285451693  

done, ty
