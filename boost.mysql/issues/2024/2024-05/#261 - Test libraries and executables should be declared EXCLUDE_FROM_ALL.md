# #261 - Test libraries and executables should be declared EXCLUDE_FROM_ALL [Closed]

> Username: pdimov  
> Created at: 2024-05-14 18:10:57 UTC  
> Updated at: 2024-05-16 10:45:58 UTC  
> Closed at: 2024-05-16 10:45:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/261  

The Boost convention is that `cmake --build .` only builds the libraries. Test libraries and executables are built with `cmake --build . --target tests`.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-05-15 10:22:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/261#issuecomment-2112134168  

Even if `BUILD_TESTING` is set?

---

## Comment 2

> Username: pdimov  
> Created at: 2024-05-15 12:31:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/261#issuecomment-2112399966  

That's the only case it matters, as otherwise they aren't there at all.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-05-15 16:37:24 UTC  
> Url: https://github.com/boostorg/mysql/issues/261#issuecomment-2112999400  

I'll change it. But what's the rationale? Not having to reconfigure with `BUILD_TESTING=OFF` if you don't want to build the tests?

---

## Comment 4

> Username: pdimov  
> Created at: 2024-05-15 16:44:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/261#issuecomment-2113011861  

Pretty much, yes.  
  
In the future, we should probably support separate build targets for a particular library's tests, too.
