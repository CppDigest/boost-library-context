# #194 - Boost.Python should be disabled if python headers not present [Closed]

> Username: djarek  
> Created at: 2018-04-15 22:46:01 UTC  
> Updated at: 2018-04-17 06:01:25 UTC  
> Closed at: 2018-04-16 04:17:32 UTC  
> Url: https://github.com/boostorg/python/issues/194  

Boost.Python should be disabled if python headers are not present in the build environment.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-04-16 04:17:32 UTC  
> Url: https://github.com/boostorg/python/issues/194#issuecomment-381475726  

By "disabled" I assume you are referring to `Boost.Build` and its logic to decide which Boost libraries to build. Please submit a feature request there: https://github.com/boostorg/build/issues

---

## Comment 2

> Username: djarek  
> Created at: 2018-04-17 06:01:25 UTC  
> Url: https://github.com/boostorg/python/issues/194#issuecomment-381855641  

Yes, that's what I meant. Will create an issue there.
